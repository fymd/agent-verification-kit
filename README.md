# agent-verification-kit

面向 **Trae / WorkBuddy** 的 AI 编码 Agent 验证体系。

解决三个常见痛点：

1. **频繁需要人工判断** → 把「完成标准」和验证变成可执行规则
2. **代码质量不稳** → 用固定原则 + Verifier 强制证据
3. **改着改着把正常功能改坏** → 明确边界 + 回归验证

灵感来自 Lauren Tan（Cursor / SpaceXAI）的「信任优先」工作流：先让 Agent 自己验证，再谈并行和过夜跑。

当前示例以 **Python / Go 后端** 为主，通用模板仍适用于其他语言。

---

## 仓库结构

```
agent-verification-kit/
├── README.md
├── agents/
│   └── verifier-agent.md              # Trae 自定义 Agent 系统提示词
├── templates/
│   ├── task-with-boundary.md          # 通用任务模板（带边界保护）
│   └── task-python-go.md              # Python/Go 后端向任务模板
└── examples/
    ├── feature-map-example.md         # 通用 Feature Map 结构 + 前端列表示例
    ├── feature-map-python-api.md      # Python API / 状态机示例
    └── feature-map-go-service.md      # Go worker / 幂等与并发示例
```

---

## 快速开始（Trae）

### 1. 创建 Verifier Agent

1. 打开 Trae → 自定义 Agent
2. 把 `agents/verifier-agent.md` 的内容完整粘贴为系统提示词
3. 保存，命名为 `Verifier` 或 `验收专家`

### 2. 每次任务用模板

- 通用：`templates/task-with-boundary.md`
- **Python / Go 后端优先用**：`templates/task-python-go.md`

填好目标和验收标准后发给 Builder。要求：
- Builder 做完后必须把结果交给 Verifier
- Verifier 返回 `PASS` / `FAIL` / `BLOCKED` + 证据后，你再审

### 3. 给关键模块补 Feature Map

| 场景 | 参考文件 |
|------|----------|
| 结构说明 + 通用写法 | `examples/feature-map-example.md` |
| Python API / 状态机 / 幂等 | `examples/feature-map-python-api.md` |
| Go consumer / 条件更新 / race | `examples/feature-map-go-service.md` |

Agent 改代码前必须先读对应 Feature Map。

---

## Python / Go 特别注意

| 风险点 | 建议写进 Feature Map / 验收标准 |
|--------|----------------------------------|
| 状态机被放宽 | 明确合法迁移表；禁止的路径要有测试 |
| 幂等失效 | 「重复请求不重复写流水、不重复发事件」写成验收项 |
| 先读后写无条件 | 要求条件更新 / 乐观锁；Go 可要求 `-race` 测试 |
| 改坏公开契约 | 「不能破坏」里写清路径、字段、错误码 |
| 只改逻辑不跑测试 | 验证方式固定为 `pytest` 或 `go test` 并贴输出 |

---

## 核心原则（简版）

| 原则 | 说明 |
|------|------|
| 没有证据的完成 = 未完成 | Verifier 必须看到测试输出或可复现步骤 |
| 最小改动 | 只改完成目标所必需的文件和行 |
| 明确边界 | 写清「可以改什么」「绝对不能动什么」 |
| 回归优先 | 改完必须验证原有关键路径与现有测试 |
| 失败要复盘 | 每次被改坏，更新 Feature Map |

---

## 推荐工作流

```
你 → 任务模板（Python/Go 版）
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
