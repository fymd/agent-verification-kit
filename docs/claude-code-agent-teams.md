# Claude Code Agent Teams 与本仓库对照

参考官方文档：[协调 Claude Code 会话团队（Agent teams）](https://code.claude.com/docs/zh-CN/agent-teams)

Agent teams 为**实验性功能**，默认关闭。启用后：一个会话当 **lead（负责人）**，多个 **teammate（队友）** 各自独立 context，可共享任务列表并直接互相通信；你也可不经过负责人直接与任一队友对话。

---

## 1. 核心概念摘要

| 概念 | 含义 |
|------|------|
| Lead | 协调工作、分配任务、综合结果 |
| Teammate | 独立会话 + 独立 context；可互发消息 |
| 共享任务列表 | 有 Task 工具的会话中认领/更新任务 |
| 与 Subagents 区别 | Subagent 向调用者回传结果；Team 队友彼此协作、token 成本更高 |

**更适合用 Agent teams 的场景**（官方强调）：

- 研究与审查：多角度并行调查再交叉质疑  
- 新模块/功能：每人独立一块、互不抢同一文件  
- 竞争假设调试：并行验证不同根因理论  
- 跨层改动：前端 / 后端 / 测试分给不同队友  

**不适合**：强顺序依赖、多人同时改同一文件、纯串行流水线 → 用单会话或 [Subagents](https://code.claude.com/docs/zh-CN/sub-agents) 更划算。

---

## 2. 启用（Claude Code）

在 `settings.json` 或环境中：

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

注意：

- 启用后，Claude **自行命名的子代理**可能直接以**队友**启动（而不只是 subagent）。  
- 需要**交互式会话**；`-p` 非交互 / 部分 SDK 场景下不会生成队友。  
- 显示：`teammateMode` 可为 `in-process`（默认）或 `auto` / `tmux` / `iterm2` 分屏。  
- 权限请求会冒泡到 lead，生成前尽量配好 permission，减少打断。  
- 存在会话恢复、任务协调、关闭行为等**已知限制**（见官方 Limitations）。

关闭团队行为示例：将变量设为 `"0"`。

---

## 3. 与本仓库「软件团队」角色映射

| Claude Agent teams | 本仓库 team/ | 说明 |
|--------------------|--------------|------|
| Lead | [orchestrator.md](../team/orchestrator.md) | 质量门、进度、分派；综合结果 |
| Teammate: Spec | [experts/spec.md](../team/experts/spec.md) | 验收标准与边界 |
| Teammate: Backend | [experts/backend.md](../team/experts/backend.md) | 实现；文件所有权要隔离 |
| Teammate: Debug | [experts/debug.md](../team/experts/debug.md) | 可并行竞争假设调试 |
| Teammate: Verifier | [experts/verifier.md](../team/experts/verifier.md) | **独立验收**；不实现 |
| 共享任务列表 | [task-card.md](../team/task-card.md) + 看板 | 语义对齐官方 Task list |

**质量规则（本仓库强制，建议写进 lead 提示）：**

1. Backend 完成 ≠ Done，必须经 Verifier PASS。  
2. 同一热点文件/同一状态机同时只允许一个实现向队友。  
3. Verifier 不写业务代码；证据不足 → BLOCKED。  
4. 先串行跑通 lead→实现→验收，再开并行队友。

---

## 4. 启动话术示例（Claude Code）

在已启用 Agent teams 的会话中对 lead 说：

```text
创建 agent team。你是 lead，按仓库 team/orchestrator.md 的职责工作。
生成这些队友（各自独立，不要改同一批文件）：
1) Spec — 使用 team/experts/spec.md 的约束
2) Backend — team/experts/backend.md，只做实现与测试输出
3) Verifier — team/experts/verifier.md，只做 PASS/FAIL/BLOCKED

本次目标：
<一句话>

规则：
- 共享任务列表；实现类任务完成后必须建「验收」任务给 Verifier
- 无 pytest/go test 等证据不得标完成
- 高风险（鉴权/支付/迁移）先请示我
```

研究/调试并行示例：

```text
Spawn 3 teammates to debug <现象> with competing hypotheses:
one assumes race/condition update, one assumes wrong status transition,
one assumes event published without idempotency. Share findings, then
hand a minimal fix plan to lead. Do not all edit the same files.
```

计划后再实现（官方建议）：先让 lead 进入计划模式再 spawn，队友先出计划再动代码。

---

## 5. Subagents vs Agent teams vs WorkBuddy

| 方式 | 协调者 | 彼此通信 | 本仓库用法 |
|------|--------|----------|------------|
| Subagents | 主会话收回结果 | 通常不横向协作 | 单次「跑测试/搜代码」可当 subagent 定义 |
| Agent teams | Lead + 共享任务 | 队友可互信/互质询 | 对应 team/ 全角色；适合独立模块并行 |
| WorkBuddy 专家组 | 你或「研发主控」专家 | 靠你粘贴交接 | [workbuddy-setup.md](../team/workbuddy-setup.md) |

角色提示词**跨工具可复用**：同一套 `team/experts/*.md`，在 Claude Code 里当 teammate 人设，在 WorkBuddy 里当「我的专家」。

可选：把各专家正文放到 Claude 的 `.claude/agents/` 作为 subagent 定义，再在 team 中按类型 spawn（见官方 “use subagent definitions for teammates”）。

---

## 6. 落地检查清单

- [ ] 仅在「可并行、文件所有权清晰」时开 Agent teams  
- [ ] Lead 使用 orchestrator 质量门  
- [ ] 固定有 Verifier 队友或验收任务  
- [ ] 预配置权限，减少队友权限弹窗堵死 lead  
- [ ] 控制队友数量；token 会明显高于单会话  
- [ ] 顺序型小修复：关掉 teams，用单会话 + Verifier 提示即可  

---

## 7. 官方链接

- Agent teams（中文）：https://code.claude.com/docs/zh-CN/agent-teams  
- Subagents：https://code.claude.com/docs/zh-CN/sub-agents  
- 并行方式总览：https://code.claude.com/docs/en/agents  
