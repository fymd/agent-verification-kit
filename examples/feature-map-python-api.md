# Feature Map 示例（Python / API 服务）

适合：FastAPI / Flask / Django 等后端服务中，容易被 Agent 改坏的接口或业务模块。

---

```markdown
# 订单状态更新接口

## 目的
将订单从「待支付」推进到「已支付」或「已取消」，并保证状态机合法、幂等、可审计。

## 入口
- HTTP：`POST /api/v1/orders/{order_id}/status`
- 内部调用：`OrderService.transition(order_id, target_status, actor)`
- 触发方：支付回调、管理后台、超时取消任务

## 核心行为（预期）
1. 仅允许合法迁移：
   - pending → paid
   - pending → cancelled
   - 其他迁移返回 409 或业务错误码
2. 同一 order_id + 同一 target_status 重复请求幂等（不重复写流水、不重复发事件）
3. 成功后写入状态变更流水，并发送领域事件（如 OrderPaid）
4. 权限：只有订单所属用户或具备 admin 角色可操作

## 代码入口（导航用）
- 路由：`app/api/v1/orders.py` → `update_order_status`
- 服务：`app/services/order_service.py` → `transition`
- 状态机：`app/domain/order_state.py`
- 仓储：`app/repositories/order_repo.py`
- 事件：`app/events/order_events.py`

## 边界与约束
### 可以改
- `order_service.transition` 内的校验与迁移逻辑
- 与本接口直接相关的错误码与返回体
- 对应的单元测试 / 集成测试

### 绝对不能动
- 支付渠道回调验签逻辑
- 用户鉴权中间件（除非任务明确要求）
- 其他聚合的状态机（如退款、库存）
- 数据库迁移脚本（除非任务包含 schema 变更）

### 隐式约定（Agent 易忽略）
- 状态字段存在 DB 与缓存两处时，以 DB 事务为准，缓存异步失效
- 流水表 `order_status_logs` 只追加，不更新、不删除
- `paid` 后不允许再 `cancelled`（需走退款流程，不在本接口）
- 错误码与 HTTP 状态有固定映射，不要随意改文案导致前端依赖断裂

## 回归检查清单
- [ ] pending → paid 成功，流水 + 事件正常
- [ ] pending → cancelled 成功
- [ ] paid → cancelled 被拒绝
- [ ] 重复 paid 请求幂等
- [ ] 无权限用户调用返回 403
- [ ] 相关单测：`tests/services/test_order_service.py` 全部通过

## 常见失败模式
- 现象：重复回调导致重复发货/重复记账  
  原因：只改了状态判断，忘了幂等键或流水去重  
  应避免：改状态机时不同步看事件与流水写入

- 现象：管理后台能取消已支付订单  
  原因：放宽了迁移表，未区分「用户取消」与「退款」  
  应避免：用本接口承载退款语义

## 验证命令示例
```bash
# 单测
pytest tests/services/test_order_service.py -q

# 本地手动（示例）
curl -X POST "$BASE/api/v1/orders/$OID/status" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"status":"paid"}'
```

## 维护说明
状态枚举、合法迁移表、错误码映射变更时，同步更新本文档与测试。
```

---

## 使用提示（Python）

- 把 Feature Map 放在 `docs/features/` 或模块旁的 `FEATURE.md`。
- 任务模板里写上路径，要求 Agent：**先读 Feature Map，再读代码，再改**。
- 验证优先跑**现有 pytest**；没有测试时，至少要求给出可重复的 curl / 脚本步骤。
