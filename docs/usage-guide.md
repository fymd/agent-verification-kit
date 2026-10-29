# 使用指导（全局能力 × 项目记忆）

本文是 **agent-verification-kit** 的统一使用说明。先分清两层，再按步骤接入。

---

## 1. 两层模型（必读）

| 层 | 放什么 | 放哪里 |
|----|--------|--------|
| **全局（跨项目）** | 角色提示词、一次做对规则、任务卡/Failure/Playbook **空模板**、通用禁忌、本 kit 文档 | WorkBuddy「我的专家」、用户级 Agent、本 GitHub 仓库 |
| **项目（每个业务仓库）** | `VERIFY.md`、`docs/features/`、`docs/failures/`、带真实命令的 playbooks | **该业务仓库根目录**，不是本 kit 仓库 |

- 全局解决：**怎么协作、怎么验收、怎么不二犯（流程）**  
- 项目解决：**这个代码库边界是什么、曾失败过什么、怎么跑测试（记忆）**  

只配全局、不建项目目录 → 主控「记忆门」无内容可检索，一次做对会失效。  
只建项目目录、不配专家提示词 → 没有稳定角色与质量门。

---

## 2. 路径对照

### 2.1 本 kit（模板源）

```text
https://github.com/fymd/agent-verification-kit
```

| 用途 | 路径 |
|------|------|
| 主控 | `team/orchestrator.md` |
| 验收 | `team/experts/verifier.md` 或 `agents/verifier-agent.md` |
| 其他专家 | `team/experts/*.md` |
| WorkBuddy 步骤 | `team/workbuddy-setup.md` |
| 完整任务卡 | `templates/task-card-full.md` |
| Failure / Playbook 空模板 | `templates/failure-record.md`、`templates/success-playbook.md` |
| VERIFY 技能模板 | `skills/project-verification-skill.md` |
| Feature 骨架 | `skeletons/FEATURE-*.md` |
| 一次做对宪法 | `docs/first-time-right.md` |
| 业务仓接入清单 | `docs/project-bootstrap.md` |
| Claude Teams 对照 | `docs/claude-code-agent-teams.md` |

### 2.2 业务仓库（真实落地）

```text
<your-app>/                 # 你的 Python/Go 项目根目录
├── VERIFY.md
└── docs/
    ├── features/
    ├── failures/
    └── playbooks/
```

不要把上述目录建在 `agent-verification-kit` 里当作「唯一全局 failures」——除非你单独维护组织级失败库并按仓库名过滤（高级用法，见文末）。

---

## 3. 推荐接入顺序

### 步骤 A：全局（做一次）

1. 收藏/克隆本仓库作手册。  
2. WorkBuddy：按 `team/workbuddy-setup.md` 创建至少 **研发主控**、**验收专家**（提示词从 `team/` 复制）。  
3. 可选：Backend / Spec / Debug；Claude 则复制 `team/claude-agents/` → `~/.claude/agents/` 或项目 `.claude/agents/`。  

### 步骤 B：每个业务项目（每个仓库一次）

1. 建目录：`docs/features`、`docs/failures`、`docs/playbooks`。  
2. 从 `skills/project-verification-skill.md` 复制为根目录 `VERIFY.md`，改成真实 `pytest` / `go test` / 健康检查。  
3. 对易改坏模块，从 `skeletons/` 复制一份到 `docs/features/<模块>.md` 并填空。  
4. 对主控说明：本仓库路径 + 「按 VERIFY.md 与 docs/features 执行记忆门」。  

### 步骤 C：日常任务

1. 只跟 **研发主控** 说目标。  
2. 主控出完整任务卡（Spec 门 + 记忆门）。  
3. Backend/Debug → 证据 → Verifier。  
4. FAIL → `docs/failures/` + 防再犯落地 → 再修。  
5. PASS → 可重复则写 `docs/playbooks/`。  

---

## 4. 全局 Playbook vs 项目 Playbook

| 类型 | 内容 | 位置 |
|------|------|------|
| 全局流程型 | 步骤、禁忌、角色顺序（不含具体包路径） | 可放本 kit `examples/` 或组织 Wiki |
| 项目可执行型 | 真实测试命令、模块路径、环境变量 | 业务仓 `docs/playbooks/` |

项目 Playbook 可写：「流程见 kit examples/playbook-go-bugfix.md；本仓命令如下：…」。

---

## 5. 组织级「全局 failures」可选方案

若多个仓库想共用一个失败库：

1. 单独仓库如 `org-agent-memory`，目录 `failures/<repo-name>/...`。  
2. 每条记录必填：**仓库名、模块路径、检索关键词**。  
3. 主控记忆门：先过滤当前仓库名，再搜关键词。  
4. 业务仓仍保留 `docs/features` + `VERIFY.md`（边界与命令不能外置）。  

未做过滤的「一个大杂烩 failures」容易误用，不推荐作为默认。

---

## 6. 完成定义（任务级）

- [ ] Verifier = PASS（有证据）  
- [ ] 若曾 FAIL：Failure 防再犯已勾选  
- [ ] Playbook：已写或明确「无需」  
- [ ] 看板状态 Done  

---

## 7. 相关文档

- [project-bootstrap.md](project-bootstrap.md) — 业务仓清单  
- [first-time-right.md](first-time-right.md) — 门禁与防二犯  
- [../team/README.md](../team/README.md) — 团队角色  
- [../README.md](../README.md) — 仓库入口  
