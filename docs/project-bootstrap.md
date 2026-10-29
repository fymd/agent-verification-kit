# 业务仓库接入清单（一次性）

完整说明见 **[usage-guide.md](usage-guide.md)**（全局 vs 项目）。  
本文只列出：**在你的业务项目根目录**要建的内容和复制来源。

> 这些目录建在 **业务仓库**，不是 `agent-verification-kit`。  
> 专家提示词与流程模板可全局配置；`VERIFY` / features / failures 必须按项目落地。

## 1. 目录（业务仓库根）

```text
<your-app>/
├── VERIFY.md
└── docs/
    ├── features/          # Feature Map
    ├── failures/          # Failure Record
    ├── playbooks/         # Success Playbook
    └── agent-board.md     # 可选
```

## 2. 从 kit 复制什么

| 来源（本 kit） | 放到业务仓库 |
|----------------|--------------|
| `skills/project-verification-skill.md` | `VERIFY.md`（改成真实命令） |
| `skeletons/FEATURE-*.md` | `docs/features/<模块>.md` |
| `templates/failure-record.md` | 需要时填到 `docs/failures/` |
| `templates/success-playbook.md` | 需要时填到 `docs/playbooks/` |
| `templates/task-card-full.md` | 建卡时复制使用 |
| `examples/*` | 仅对照，勿当本项目正式记录 |

## 3. 全局（与项目分开）

| 全局 | 方式 |
|------|------|
| 研发主控 / 验收等专家 | WorkBuddy「我的专家」← `team/*.md` |
| 一次做对规则 | 主控提示词已含；详见 `docs/first-time-right.md` |
| 本手册 | 保持 git clone/收藏本仓库即可 |

## 4. WorkBuddy

1. [team/workbuddy-setup.md](../team/workbuddy-setup.md)  
2. 主控：`team/orchestrator.md`  
3. 验收：`team/experts/verifier.md`  

## 5. 第一条任务

1. 告诉主控：业务仓库路径 + 目标  
2. 完整任务卡（Spec 门 + 记忆门）  
3. 实现 → Verifier  
4. FAIL → `docs/failures/` + 防再犯  
5. PASS → 可重复则 `docs/playbooks/`  

## 6. 完成定义

Verifier PASS；若曾 FAIL 则防再犯已落地；Playbook 已写或明确无需。
