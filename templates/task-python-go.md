# 任务模板变体（Python / Go 后端）

在通用模板基础上，补充后端常见验收与验证方式。  
复制后按项目填写；仍建议完成后交给 Verifier。

---

## 模板

```markdown
## 任务目标
（一句话）

## 背景 / 上下文
- 语言/模块：Python | Go
- 相关包/路径：
- 相关 Feature Map：
- 相关测试目录：

## 验收标准
1. 
2. 
3. 
（尽量写成可断言的行为：状态、返回码、幂等、错误类型）

## 不能破坏
- 接口契约（路径、字段、错误码）
- 幂等与并发行为（如有）
- 现有测试：`pytest ...` 或 `go test ...` 必须保持通过
- 具体功能：
  - ...

## 允许修改的范围
- 可以改：
- 绝对不能动：
- 约束：最小 diff；不随意加依赖；不改公开 API 除非任务明确要求并说明

## 验证方式（必须附证据）
### Python 示例
```bash
pytest tests/path/to/test_xxx.py -q
# 或
pytest tests/ -k "order" -q
```

### Go 示例
```bash
go test ./internal/service/ -count=1
go test ./internal/worker/ -count=1
# 涉及并发时
go test ./internal/service/ -race -count=1
```

### 手动/集成（如需要）
- curl / grpcurl 步骤
- 预期 HTTP/gRPC 状态与 body 关键字段

## 交付要求
- 改动文件列表 + 原因（尤其是条件更新、幂等、事务边界）
- 测试命令与完整输出摘要
- 若有行为变更：旧行为 → 新行为对照
- 交给 Verifier，不要自行宣布完成
```

---

## 填写示例（Go：超时取消幂等）

```markdown
## 任务目标
修复订单超时取消 consumer 在重复消息下重复发送 OrderCancelled 事件的问题。

## 背景
- Go
- Feature Map：docs/features/order-timeout-cancel.md
- 测试：internal/service、internal/worker

## 验收标准
1. pending 订单首次超时消息：取消成功，恰好一条 timeout 流水与一条事件
2. 同一订单再次收到超时消息：状态保持 cancelled，不再写流水、不再发事件
3. 已 paid 订单收到超时消息：保持 paid，无取消事件

## 不能破坏
- CancelIfPending 对 pending→cancelled 的正常路径
- 支付回调 paid 路径
- go test ./internal/service/ 与 ./internal/worker/ 全部通过

## 允许修改的范围
- 可以改：CancelIfPending 及事件发布条件
- 不能动：支付回调、其他 worker、公开 HTTP 取消 API 契约

## 验证方式
```bash
go test ./internal/service/ -count=1 -v
go test ./internal/worker/ -count=1 -v
go test ./internal/service/ -race -count=1
```

## 交付要求
- 说明如何判断「状态未变化则不发事件」
- 贴测试输出
- 交 Verifier
```

---

## 填写示例（Python：状态机迁移）

```markdown
## 任务目标
禁止订单从 paid 直接 cancelled，必须走退款流程；pending→cancelled 保持可用。

## 验收标准
1. pending → cancelled 成功
2. paid → cancelled 返回业务错误（明确错误码），状态不变
3. 现有 pending → paid 与幂等 paid 行为不变

## 不能破坏
- tests/services/test_order_service.py 中与 paid、幂等相关用例
- 支付回调入口

## 验证方式
```bash
pytest tests/services/test_order_service.py -q
```

## 交付要求
- 说明合法迁移表改动
- 贴 pytest 输出
- 交 Verifier
```
