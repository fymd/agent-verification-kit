# 优化评估与后续路线图

基于当前仓库完成度评估：**流程与模板已齐，默认不必再扩角色**；优先在业务仓跑通闭环，再按痛点加码。

---

## 已足够（可先用）

- [x] Verifier + 证据门  
- [x] 软件团队角色与主控质量门  
- [x] 一次做对 / Failure / Playbook  
- [x] Python/Go 任务与 Feature 骨架  
- [x] WorkBuddy / Claude 对照  
- [x] 全局 vs 项目分层说明（usage-guide）  
- [x] 业务仓 bootstrap  

---

## 建议优先优化（按收益）

### P0 — 用起来才暴露的问题（在业务仓做）

| 项 | 说明 |
|----|------|
| 真实 VERIFY.md | 模板需改成你的 pytest/go test，否则验收空转 |
| 1～2 个核心 Feature Map | 只覆盖最易改坏的模块，不必全库 |
| 主控提示词与 WorkBuddy 同步 | 确保用的是最新 orchestrator（含防二犯） |
| 第一条 Failure 真归档 | 体系是否有效，看第一次 FAIL 有没有入库 |

### P1 — kit 侧可增强（有痛点再做）

| 项 | 说明 |
|----|------|
| Trae 专用一页配置说明 | 与 WorkBuddy 并列，减少工具切换成本 |
| 专家「交接消息」固定句式库 | 降低你在专家间复制时的遗漏 |
| Feature Map 与 failures 互链约定 | 在 skeleton 里加「相关 Failure」固定小节 |
| 中英文任务卡字段统一表 | 多工具粘贴时减少歧义 |

### P2 — 可选扩展（非必须）

| 项 | 说明 |
|----|------|
| Frontend / DevOps / 安全专家 | 有跨层需求再加，避免角色膨胀 |
| 组织级 failures 仓库 | 多项目时再上，且必须按 repo 过滤 |
| CI 里跑 VERIFY 摘要 | 把 Agent 证据与流水线对齐 |
| 自动化检索 failures 的 Skill | 依赖具体 IDE/Agent 能否搜 docs |

### 明确不必优化的方向

- 为省 token 砍角色或砍验收（与一次做对冲突）  
- 在 kit 仓库堆积「假的」业务 failures 当全局唯一真相  
- 未跑通串行闭环前大搞并行多 Backend  

---

## 健康度自检（每月）

1. 近 10 个任务有多少次「无 Spec 就写码」？目标 → 0  
2. FAIL 后有无 Failure 文件？目标 → 100% 有根因的 FAIL 都有  
3. 同类 FAIL 是否出现第二次？目标 → 有防再犯后为 0  
4. 重复型需求是否套了 Playbook？  
5. Verifier 是否仍被跳过？目标 → 0  

---

## 当前结论

**Kit 文档与模板阶段可告一段落。**  
下一阶段最大优化是：**在真实项目按 usage-guide 接入并完成 1 次全闭环**，用真实 Failure/Playbook 反哺 Map，而不是继续加空模板。
