# 成功案例与可复用做法

本文从公开实践中提炼**已经证明有效**的做法，并映射到本仓库的模板。不追求堆 Agent 数量，只追求「少人工介入、少改坏、有证据」。

---

## 1. Lauren Tan / pstack：验证是基础设施

**做法摘要**

- 验证优先：Agent 必须能**跑真实产品/接口**并证明结果，而不是只编译通过。
- Verification skill 五件套思路：Launch → Doctor → Drive → Evidence → Cleanup。
- Feature Map：从用户/调用方视角写清「怎么到达、怎么操作、预期是什么」。
- 信任曲线：先让**一个** Agent 可验证，再并行；否则并行只增加 babysitting。
- 失败模式沉淀为 Skill / Playbook，而不是每次口头纠正。

**映射到本仓库**

| pstack 思路 | 本仓库 |
|-------------|--------|
| Verifier / 证据 | `agents/verifier-agent.md` |
| 任务边界与验收 | `templates/task-*.md` |
| Feature Map | `examples/*` + `skeletons/*` |
| 项目级验证技能 | `skills/project-verification-skill.md` |

**参考**

- pstack 插件与 playbook（Cursor plugins / poteto）
- poteto 公开指南：Verification is all you need；create/maintain verification skill

---

## 2. 四层验证（静态 → 自测 → 评审 → 运行态）

行业实践把 Agent 产出的检查分成四层，漏一层就会把人绑回 diff 审查：

| 层 | 查什么 | Python/Go 常见手段 |
|----|--------|---------------------|
| 1 静态 | 类型、lint、禁止模式 | `ruff`/`mypy`，`go vet`/`staticcheck` |
| 2 Agent 自测 | 本次改动的单元/表测 | `pytest`，`go test` |
| 3 评审 Agent | diff 正确性、越界、回归风险 | 本仓库 Verifier + 人工抽查 |
| 4 运行态 | 真实接口/队列/集成行为 | curl、集成测、consumer 回放 |

**可复用规则**

- Refactor：1 + 3 + 已有回归（层 2 不必堆新用例）。
- Bugfix：四层都要，层 4 至少覆盖「复现路径」。
- 新功能：先有失败测试或明确验收命令，再实现（见下一节）。

---

## 3. Tests-first / 验收先于实现

多支团队的共同结论：**先写或扩展能描述行为的测试，人审测试，再让 Agent 实现，直到测试绿且无额外破坏。**

对后端尤其有效：

1. 任务里写清 Acceptance Criteria（可断言）。
2. Builder 先补/改测试（或给出将失败的测试），你只审测试是否表达了需求。
3. 再实现；Verifier 只认「命令 + 输出」，不认「我觉得可以」。
4. **改已有测试**必须单独标出，防止 Agent 用改测试换绿。

本仓库：`templates/task-python-go.md` 的「验证方式」和「不能破坏」就是为这一步服务的。

---

## 4. 证据包（Evidence pack）而不是口头完成

成功实践要求完成声明必须带结构化证据，例如：

- 跑了哪些命令、退出码、关键输出摘要
- 跳过了哪些检查、为什么
- 改了哪些文件、是否越界
- 置信度与未验证项

Verifier 的 `PASS|FAIL|BLOCKED` 输出格式就是一种轻量证据包。更重的团队会把证据写入 CI artifact 或 PR 模板。

---

## 5. 小批量 + 独立验证标准

- **小 PR / 小任务**：Agent 一次只碰一个边界清晰的目标；大批量必然回归多、人审变回瓶颈。
- **工具无关的质量门**：以 pytest/go test/lint/CI 为标准，而不是「某个 Agent 说 OK」。生成与验证职责分离（Builder ≠ Verifier）。
- **风险路由**：鉴权、支付、数据删除、迁移必须更高人工门槛；纯文案或测试补强可更自动。

---

## 6. Harness：一次配置，持续卡住「没验证就收工」

Claude Code / Cursor 等支持 hooks：在 stop / 提交前强制跑一条验证命令。思想与本仓库一致：

- 有一条**默认验证命令**（如 `make test` / `pytest -q` / `go test ./...`）
- Agent 不能空口结束任务
- 权限白名单减少「每条命令都问人」

在 Trae/WorkBuddy 里若没有同等 hook，就用 **Verifier 必经关卡** + 任务模板硬性要求「附测试输出」代替。

---

## 7. 针对 Python/Go 的高频成功模式

| 问题 | 成功约束 |
|------|----------|
| 改坏状态机 | Feature Map 写死合法迁移；非法路径有测试 |
| 重复消息/回调 | 幂等验收项 + 条件更新；状态未变不发事件 |
| 并发覆盖 | Go：`UpdateStatusIf` + 可选 `-race` |
| 只改逻辑不跑测 | 验证方式固定为 pytest/go test 并贴输出 |
| 扩大公开 API | 「不能破坏」写路径/字段/错误码 |

---

## 8. 建议采用顺序（与信任曲线一致）

1. 装好 Verifier + 用 `task-python-go` 跑**一个**真实小任务。  
2. 给最容易改坏的模块写 1 份 FEATURE（用 skeletons）。  
3. 固定「无测试输出 = BLOCKED」。  
4. 再考虑并行任务或过夜跑。  
5. 每次真实翻车，只更新 Feature Map / 任务模板一处，形成复利。

---

## 延伸阅读（公开来源方向）

- Lauren Tan（poteto）：pstack、verification skill、feature map、信任曲线
- 工程团队实践：tests-before-implementation、contract tests、小批量 PR
- 验证分层：static → agent tests → review agent → running product
- Agent harness：stop gate + 单一验证命令 + 权限策略

（具体 URL 随时间变化；以「验证优先、证据优先、边界写清」为准绳即可。）
