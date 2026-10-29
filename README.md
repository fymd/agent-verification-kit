# agent-verification-kit

面向 **Trae / WorkBuddy** 的 AI 编码 Agent 验证体系。

解决三个常见痛点：

1. **频繁需要人工判断** → 把「完成标准」和验证变成可执行规则
2. **代码质量不稳** → 用固定原则 + Verifier 强制证据
3. **改着改着把正常功能改坏** → 明确边界 + 回归验证

灵感来自 Lauren Tan（Cursor / SpaceXAI）的「信任优先」工作流：先让 Agent 自己验证，再谈并行和过夜跑。

---

## 仓库结构

```
agent-verification-kit/
├── README.md
├── agents/
│   └── verifier-agent.md          # Trae 自定义 Agent 系统提示词
├── templates/
│   └── task-with-boundary.md      # 带边界保护的通用任务模板
└── examples/
    └── feature-map-example.md     # Feature Map 示例
```

---

## 快速开始（Trae）

### 1. 创建 Verifier Agent

1. 打开 Trae → 自定义 Agent
2. 把 `agents/verifier-agent.md` 的内容完整粘贴为系统提示词
3. 保存，命名为 `Verifier` 或 `验收专家`

### 2. 每次任务用模板

复制 `templates/task-with-boundary.md`，填好目标和验收标准后发给 Builder Agent。

要求：
- Builder 做完后，必须把结果交给 Verifier
- Verifier 返回 `PASS` / `FAIL` / `BLOCKED` + 证据后，你才审结果

### 3. 给关键模块补 Feature Map

参考 `examples/feature-map-example.md`，给经常被改坏的模块写边界说明。
Agent 改代码前必须先读对应 Feature Map。

---

## 核心原则（简版）

| 原则 | 说明 |
|------|------|
| 没有证据的完成 = 未完成 | Verifier 必须看到运行结果、测试输出或复现步骤 |
| 最小改动 | 只改完成目标所必需的文件和行 |
| 明确边界 | 写清「可以改什么」「绝对不能动什么」 |
| 回归优先 | 改完必须验证原有关键路径仍然正常 |
| 失败要复盘 | 每次被改坏，更新 Feature Map 或 Skill |

---

## 推荐工作流

```
你 → Orchestrator / 任务模板
        ↓
     Builder（只实现）
        ↓
     Verifier（只验证，返回证据）
        ↓
你只审 PASS/FAIL 和差异（而不是每一行代码）
```

---

## 适用工具

- **Trae**：自定义 Agent + 系统提示词（推荐主力）
- **WorkBuddy**：可把 Verifier 做成专家 / Skill，任务模板直接当指令用
- 其他支持自定义 Agent / Skills 的工具也可套用

---

## License

MIT（可自由用于个人与商业项目）
