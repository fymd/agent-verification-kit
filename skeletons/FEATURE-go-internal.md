# FEATURE 骨架（Go · internal/service）

适合常见布局：`cmd/` + `internal/service` + `internal/repo` + `internal/worker`。  
建议路径：`internal/service/FEATURE_<name>.md` 或 `docs/features/<name>.md`。

---

```markdown
# <功能名>

## 目的
<一句话>

## 入口
- 对外：HTTP/gRPC 路径或 MQ topic
- 服务方法：`internal/service/<pkg>.go` → `<Method>`
- 仓储：`internal/repo/...`
- Worker（如有）：`internal/worker/...`

## 核心行为
1. 
2. 
3. 

## 并发 / 幂等（后端必填）
- 条件更新 / 乐观锁字段：
- 重复消息或重复请求时应：
- 是否需要 `go test -race`：

## 边界
### 可以改
- 

### 绝对不能动
- 其他 worker / 支付回调 / 公开 API 契约（按需写死路径）
- 

### 隐式约定
- 先提交事务再发事件，或与 outbox 一致
- 日志必须带业务主键（如 order_id）
- 

## 回归清单
- [ ] 主路径
- [ ] 幂等 / 重复消费
- [ ] 非法状态被拒绝
- [ ] `go test ./internal/service/ -count=1`
- [ ] （可选）`go test ./internal/service/ -race -count=1`

## 验证命令
```bash
go test ./internal/service/ -count=1 -v
go test ./internal/worker/ -count=1 -v
```

## 常见失败模式
- 先读后写无条件更新 → 并发下状态被盖掉
- 状态未变仍发事件 → 下游重复处理

## 维护
消息格式、状态枚举、事件策略变更时同步本文与测试。
```
