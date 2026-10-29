# 业务仓库接入清单（一次性）

在你的 Python/Go 项目里按本清单落地，之后主要靠流程复利，而不是反复改 Agent 提示词。

## 1. 目录

```text
docs/
  features/          # Feature Map（每模块一份）
  failures/          # Failure Record
  playbooks/         # Success Playbook
  agent-board.md     # 可选：当前看板快照
VERIFY.md            # 项目验证技能（从 kit skills 复制改写）
```

## 2. 从本 kit 复制什么

| 来源 | 放到业务仓库 |
|------|----------------|
| `skills/project-verification-skill.md` | `VERIFY.md`（改成真实命令） |
| `skeletons/FEATURE-*.md` | `docs/features/<模块>.md` |
| `templates/failure-record.md` | 需要时往 `docs/failures/` 填 |
| `templates/success-playbook.md` | 需要时往 `docs/playbooks/` 填 |
| `templates/task-card-full.md` | 主控/人类建卡用 |
| `examples/failure-*.md` / `playbook-*.md` | 对照，勿原样当生产记录 |

## 3. WorkBuddy

1. 按 `team/workbuddy-setup.md` 建专家（至少：**研发主控** + **验收专家**）  
2. 主控提示词用最新 `team/orchestrator.md`  
3. 验收用 `team/experts/verifier.md`  

## 4. Claude Code（可选）

1. 复制 `team/claude-agents/*.md` → `.claude/agents/`  
2. 需要并行时再开 Agent teams（见 `docs/claude-code-agent-teams.md`）  
3. Lead 遵守 `docs/first-time-right.md`  

## 5. 第一条真实任务怎么跑

1. 对主控粘贴目标 + 仓库路径  
2. 主控出完整任务卡（Spec/记忆门打勾）  
3. Backend/Debug → 证据  
4. Verifier → PASS/FAIL  
5. FAIL：写 `docs/failures/...` 并改 Feature Map 后再修  
6. PASS：重复型工作写 `docs/playbooks/...`  

## 6. 完成定义（项目级）

- 功能：Verifier PASS  
- 若曾失败：Failure 防再犯已勾选  
- 可复用流程：Playbook 已存在或显式「无需」  

达到以上，才视为该任务在体系上关闭，而不仅是代码暂时能跑。
