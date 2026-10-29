# agent-verification-kit

面向 **WorkBuddy / Trae / Claude Code** 的 AI 编码验证体系与软件团队 Agent 编组。

**目标：** 一次做对、成功可复用、失败必归档、同类错误不二犯。  
技术栈示例以 **Python / Go** 为主。

---

## 你从哪开始

| 你想… | 去读 |
|--------|------|
| 接到现有业务仓库 | [docs/project-bootstrap.md](docs/project-bootstrap.md) |
| 一次做对 / 防再犯 | [docs/first-time-right.md](docs/first-time-right.md) |
| WorkBuddy 建专家 | [team/workbuddy-setup.md](team/workbuddy-setup.md) |
| 软件团队角色提示词 | [team/](team/) |
| Claude Agent Teams 对照 | [docs/claude-code-agent-teams.md](docs/claude-code-agent-teams.md) |
| 只做单任务验证 | [agents/verifier-agent.md](agents/verifier-agent.md) + [templates/task-python-go.md](templates/task-python-go.md) |

---

## 核心工作流

```
你 → 研发主控
       → Spec 门 + 记忆门（failures / Feature Map / playbooks）
       → Backend 或 Debug
       → Verifier（无证据不得 PASS）
       → FAIL：Failure Record + 防再犯落地后再修
       → PASS：可选 Playbook；更新看板
```

---

## 目录速查

```
agents/           Verifier 系统提示词
templates/        任务卡、Failure、Playbook、Python-Go 任务
examples/         Feature Map / 失败与成功示例
skeletons/        FastAPI / Go FEATURE 骨架
skills/           项目 VERIFY 技能模板
team/             主控 + 领域专家 + WorkBuddy + Claude agents
docs/             一次做对、bootstrap、Claude teams、成功实践
```

---

## 质量硬规则（摘要）

1. 无完整验收标准 → 不得实现  
2. 开干前检索 failures、读 Feature Map  
3. 实现 ≠ 完成，必须经 Verifier  
4. FAIL 必须归档并落地防再犯，否则不得关闭  
5. 同类根因第二次 FAIL → 先改体系再改代码  
6. 成功可重复流程 → 写入 playbooks  

---

## License

MIT
