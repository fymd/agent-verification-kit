# Playbook 示例：Go 服务缺陷修复（一次做对）

> 业务仓库请放到 `docs/playbooks/`。

## 适用场景

- 用：有明确复现的后端 bug；模块已有或可补 Feature Map
- 不用：需求仍含糊、无验收标准；或纯探索性重构

## 前置条件

- Spec 门已过（可判定标准 + 验证命令）
- 已搜 `docs/failures` 相关关键词
- 已读模块 Feature Map

## 标准步骤

1. Debug：稳定复现（测试或命令），写清根因与证据  
2. 若根因属于已有 Failure 类型：打开该记录，按「正确做法」实施  
3. Backend：最小 diff；条件更新/幂等等按 Map 隐式约定  
4. 跑模块测试；涉及并发则加 `-race`（若任务要求）  
5. 交 Verifier；PASS 后更新看板  
6. 若为新失败模式：写 Failure Record 并回写 Feature Map  

## 验收标准模板

1. 复现路径下错误现象消失  
2. 相关 `go test` 通过  
3. 「不能破坏」中的回归项有证据  
4. 若涉及消息/回调：重复投递不产生重复副作用  

## 验证命令

```bash
go test ./internal/service/ -count=1
go test ./internal/worker/ -count=1
```

## 禁忌

- 未复现就大面积改  
- 状态未变仍发领域事件  
- 用删测试换绿  
- 跳过 Verifier 自称完成  

## 参考

- Failure 示例：examples/failure-order-idempotency.md  
- Feature Map：examples/feature-map-go-service.md  
