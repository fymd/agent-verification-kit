# Feature Map 示例（Go 服务）

适合：Go HTTP/gRPC 服务、worker、带状态机或并发逻辑的模块。

---

```markdown
# 任务队列消费者（订单超时取消）

## 目的
消费「待支付超时」消息，将仍为 pending 的订单自动取消，并保证并发下只取消一次。

## 入口
- 消息队列 topic：`order.timeout.cancel`
- 消费者：`internal/worker/order_timeout_consumer.go`
- 核心方法：`CancelIfPending(ctx, orderID string) error`

## 核心行为（预期）
1. 仅当订单当前状态为 `pending` 时执行取消
2. 已是 `paid` / `cancelled` 时视为成功幂等，不报错、不重复写流水
3. 使用 DB 条件更新或乐观锁，避免两个消费者同时取消同一订单
4. 取消成功后写流水并发 `OrderCancelled` 事件（原因：timeout）
5. 临时错误（DB/网络）可重试；业务上不可取消则 ack 掉消息，避免死循环

## 代码入口（导航用）
- 消费者：`internal/worker/order_timeout_consumer.go`
- 领域服务：`internal/service/order_service.go` → `CancelIfPending`
- 仓储：`internal/repo/order_repo.go` → `UpdateStatusIf`
- 事件：`internal/event/order.go`
- 消息结构：`api/proto` 或 `internal/mq/types.go`

## 边界与约束
### 可以改
- `CancelIfPending` 及与超时取消直接相关的 repo 条件更新
- 本 consumer 的重试/ack/nack 策略
- 对应表测与集成测试

### 绝对不能动
- 支付成功回调把订单标为 paid 的路径
- 通用 MQ 中间件（除非任务明确要求）
- 其他 worker（发货、库存）的消费逻辑
- 公开的 HTTP/gRPC 取消接口的权限模型（可复用服务方法，但不要改 API 契约）

### 隐式约定（Agent 易忽略）
- 状态更新必须带「期望旧状态」条件，例如 `WHERE id=? AND status='pending'`
- 事件发布与 DB 提交的顺序：先提交事务再发事件，或与 outbox 模式一致；不要本地事务外先发事件
- 日志需带 `order_id` 与 `message_id`，便于追踪重复消费
- 不要在 consumer 里做耗时 HTTP 调用阻塞 ack；需要时异步解耦

## 回归检查清单
- [ ] pending 订单被正确取消，流水 reason=timeout
- [ ] 已 paid 订单收到超时消息后保持 paid，无重复事件
- [ ] 并发两个 consumer 处理同一订单，只取消一次
- [ ] 相关测试通过：`go test ./internal/service/ -count=1`
- [ ] `go test ./internal/worker/ -count=1`

## 常见失败模式
- 现象：已支付订单被超时任务取消  
  原因：更新时未带 status 条件，或先读后写无锁  
  应避免：`Get` + `Update` 无版本/条件；必须用条件更新

- 现象：重复取消事件导致下游重复退款/通知  
  原因：幂等只做了「不改状态」，仍每次发事件  
  应避免：状态未变化时仍 Publish

## 验证命令示例
```bash
# 单测
go test ./internal/service/ -count=1 -v
go test ./internal/worker/ -count=1 -v

# 竞态相关（若有）
go test ./internal/service/ -race -count=1
```

## 维护说明
消息格式、状态枚举、outbox/事件策略变更时，同步更新本文档与测试。
```

---

## 使用提示（Go）

- Feature Map 建议与包结构对应，例如 `internal/service/FEATURE_order.md`。
- 强调 **条件更新 / 幂等 / race**，这是 Go 服务里 Agent 最容易改坏的点。
- 任务里写明：改完必须跑 `go test`（必要模块 + 可选 `-race`），证据贴测试输出。
