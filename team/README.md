# 软件团队 Agent（主控 + 领域专家）

面向 **WorkBuddy / Trae** 的多 Agent 软件交付编组：一个 **主控 Orchestrator** 负责质量、进度与分派；若干 **领域专家** 只做本职；**验收专家** 作为质量闸门。

```
你
 └─ Orchestrator（主控）
      ├─ 规划 / 拆分 / 进度看板
      ├─ Spec 专家（需求澄清与验收标准）
      ├─ Backend 专家（Python/Go 实现）
      ├─ Debug 专家（复现与根因）
      └─ Verifier 验收专家（PASS/FAIL/BLOCKED）
```

## 设计原则

1. **主控不写大段业务代码**：主控负责任务分解、指派、催验收、汇总风险与进度。
2. **实现与验收分离**：Builder（Backend）不能自己宣布完成；必须过 Verifier。
3. **边界写在任务卡上**：每张卡带目标、验收标准、不能破坏项、允许改动范围。
4. **证据优先**：无测试/命令输出不得 PASS。
5. **先串行可信，再考虑并行**：Verifier 稳定前不要开多个实现专家同时改同一模块。

## 目录

| 文件 | 角色 |
|------|------|
| [orchestrator.md](orchestrator.md) | 主控：质量、进度、分派 |
| [experts/spec.md](experts/spec.md) | 需求/验收标准专家 |
| [experts/backend.md](experts/backend.md) | Python/Go 实现专家 |
| [experts/debug.md](experts/debug.md) | 排查复现专家 |
| [experts/verifier.md](experts/verifier.md) | 验收专家（与 agents/verifier-agent 对齐） |
| [workbuddy-setup.md](workbuddy-setup.md) | WorkBuddy 创建步骤与召唤话术 |
| [task-card.md](task-card.md) | 团队统一任务卡模板 |
| [progress-board.md](progress-board.md) | 进度与质量看板模板 |

## 推荐工作流

```
1. 你把目标丢给 Orchestrator
2. Orchestrator → Spec：补全验收标准与边界（必要时问你）
3. Orchestrator 建任务卡，指派 Backend 或 Debug
4. Backend/Debug 交付：改动说明 + 验证命令输出
5. Orchestrator 转交 Verifier
6. PASS → 主控更新看板并向下一项；FAIL → 退回实现并写明修复点
7. 你只处理：需求决策、FAIL 争议、高风险变更放行
```

## 与现有 kit 的关系

- 单任务模板仍可用：`templates/task-python-go.md`
- Feature Map：`examples/`、`skeletons/`
- 项目验证技能：`skills/project-verification-skill.md`
- 本目录把上述能力**编成固定团队角色**，方便在 WorkBuddy 建成专家团或一组「我的专家」。
