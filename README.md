# agent-verification-kit

面向 **Trae / WorkBuddy** 的 AI 编码 Agent 验证体系与**软件团队多专家编组**。

解决三个常见痛点：

1. **频繁需要人工判断** → 把「完成标准」和验证变成可执行规则
2. **代码质量不稳** → 用固定原则 + Verifier 强制证据
3. **改着改着把正常功能改坏** → 明确边界 + 回归验证

灵感与对照来自公开成功实践（Lauren Tan / pstack 验证优先、四层验证、tests-first、证据包、agent harness 等），详见 [docs/success-cases.md](docs/success-cases.md)。

当前以 **Python / Go 后端** 为主。

---

## 软件团队 Agent（主控 + 领域专家）

完整编组见 **[team/](team/)**：

| 角色 | 文件 |
|------|------|
| 研发主控 Orchestrator（质量/进度/分派） | [team/orchestrator.md](team/orchestrator.md) |
| Spec 需求规格 | [team/experts/spec.md](team/experts/spec.md) |
| Backend 实现 | [team/experts/backend.md](team/experts/backend.md) |
| Debug 排查 | [team/experts/debug.md](team/experts/debug.md) |
| Verifier 验收 | [team/experts/verifier.md](team/experts/verifier.md) |
| WorkBuddy 创建步骤 | [team/workbuddy-setup.md](team/workbuddy-setup.md) |

主控强制：**实现必须经验收才能标完成**；用任务卡与看板管进度。

---

## 仓库结构

```
agent-verification-kit/
├── team/                                 # 软件团队多专家（主控+领域）
├── agents/verifier-agent.md
├── templates/
├── examples/
├── skeletons/
├── skills/project-verification-skill.md
└── docs/success-cases.md
```

---

## 快速开始

### WorkBuddy（团队）

按 [team/workbuddy-setup.md](team/workbuddy-setup.md) 创建 5 个「我的专家」，日常只召唤 **研发主控**。

### Trae / 单 Agent

1. Verifier：[`agents/verifier-agent.md`](agents/verifier-agent.md)
2. 任务模板：[`templates/task-python-go.md`](templates/task-python-go.md)
3. FEATURE 骨架：[`skeletons/`](skeletons/)
4. 项目 VERIFY：[`skills/project-verification-skill.md`](skills/project-verification-skill.md)

---

## 推荐工作流（团队）

```
你 → 研发主控
       → Spec（补验收标准）
       → Backend / Debug
       → Verifier（质量闸门）
       → 主控更新看板
你只处理决策与高风险放行
```

---

## License

MIT
