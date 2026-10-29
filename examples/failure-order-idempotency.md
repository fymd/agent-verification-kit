# Failure 示例：订单超时取消重复发事件

> 业务仓库请放到 `docs/failures/`。本文件为可对照范例。

- 日期：2026-10-29
- 任务 ID：T-demo-01
- 模块：order timeout consumer（Go）
- 严重度：P1
- 相关 Feature Map：examples/feature-map-go-service.md

## 现象

同一 `order_id` 的超时消息被消费两次后，下游收到两条 `OrderCancelled`，重复通知。

## 错误做法

实现只保证「状态已是 cancelled 时不再改库」，但**每次消费仍 Publish 事件**。

## 根因

幂等只覆盖了状态写入，未覆盖事件发布；缺少「本次是否真正完成了 pending→cancelled 迁移」的分支。

## 正确做法

条件更新 `WHERE id=? AND status='pending'`；仅当 `rows_affected == 1` 时写流水并发事件；否则视为幂等成功且不发事件。

## 防再犯

- [x] 更新 Feature Map：隐式约定与常见失败模式（见 feature-map-go-service）
- [x] 测试：重复消息断言事件次数为 1
- [x] Playbook 禁忌：状态未变禁止 Publish
- [x] 默认验收：增加「重复请求不重复事件」

## 防再犯如何验证

```bash
go test ./internal/service/ -count=1 -run Idempotent
```

## 检索关键词

order, timeout, cancel, 幂等, 重复事件, OrderCancelled, consumer
