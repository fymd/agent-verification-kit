# agent-verification-kit

面向 **WorkBuddy / Trae / Claude Code** 的 AI 编码验证体系与软件团队 Agent 编组。

**目标：** 一次做对、成功可复用、失败必归档、同类错误不二犯。  
技术栈示例以 **Python / Go** 为主。

---

## 使用指导（先读）

**[docs/usage-guide.md](docs/usage-guide.md)** — 全局能力 vs 项目记忆、路径对照、接入顺序、完成定义。

| 你想… | 文档 |
|--------|------|
| 统一怎么用 | [docs/usage-guide.md](docs/usage-guide.md) |
| 接到业务仓库 | [docs/project-bootstrap.md](docs/project-bootstrap.md) |
| 一次做对 / 防再犯 | [docs/first-time-right.md](docs/first-time-right.md) |
| WorkBuddy 建专家 | [team/workbuddy-setup.md](team/workbuddy-setup.md) |
| 团队角色提示词 | [team/](team/) |
| 还要不要继续改 kit | [docs/roadmap.md](docs/roadmap.md) |
| Claude Agent Teams | [docs/claude-code-agent-teams.md](docs/claude-code-agent-teams.md) |

### 两层记一句

- **全局：** 专家提示词、流程、空模板 → WorkBuddy / 本仓库  
- **每个业务项目：** `VERIFY.md` + `docs/features|failures|playbooks` → **该项目根目录**  

---

## 核心工作流

```
你 → 研发主控
       → Spec 门 + 记忆门
       → Backend / Debug
       → Verifier
       → FAIL：Failure + 防再犯 → 再修
       → PASS：可选 Playbook
```

---

## 目录速查

```
agents/      Verifier
templates/   任务卡、Failure、Playbook、Python-Go 任务
examples/    Feature / 失败 / 成功示例
skeletons/   FastAPI、Go FEATURE 骨架
skills/      VERIFY 技能模板
team/        主控与领域专家、WorkBuddy、Claude agents
docs/        使用指导、一次做对、bootstrap、路线图、Claude teams
```

---

## License

MIT
