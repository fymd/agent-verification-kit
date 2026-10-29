# Claude Code 用 Agent 定义

将本目录下的 `*.md` **复制到** 项目的 `.claude/agents/`（或用户级 agents 目录），即可作为 [Subagents](https://code.claude.com/docs/zh-CN/sub-agents) 定义；在启用 [Agent teams](https://code.claude.com/docs/zh-CN/agent-teams) 时，也可按类型名 spawn 为 teammate。

| 文件 | 角色 |
|------|------|
| [verifier.md](verifier.md) | 验收 |
| [backend.md](backend.md) | Python/Go 实现 |
| [spec.md](spec.md) | 需求与验收标准 |
| [debug.md](debug.md) | 排查 |
| [orchestrator.md](orchestrator.md) | 说明：lead 通常用主会话 + team/orchestrator 提示，不必当 subagent |

完整流程与启用方式见 [docs/claude-code-agent-teams.md](../../docs/claude-code-agent-teams.md)。

与 WorkBuddy 共用同一套职责：提示词正文与 `team/experts/` 保持一致，仅增加 Claude frontmatter（name / description）。
