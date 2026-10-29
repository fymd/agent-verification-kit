# agent-verification-kit

面向 **Trae / WorkBuddy** 的 AI 编码 Agent 验证体系。

解决三个常见痛点：

1. **频繁需要人工判断** → 把「完成标准」和验证变成可执行规则
2. **代码质量不稳** → 用固定原则 + Verifier 强制证据
3. **改着改着把正常功能改坏** → 明确边界 + 回归验证

灵感与对照来自公开成功实践（Lauren Tan / pstack 验证优先、四层验证、tests-first、证据包、agent harness 等），详见 [docs/success-cases.md](docs/success-cases.md)。

当前以 **Python / Go 后端** 为主。

---

## 仓库结构

```
agent-verification-kit/
├── README.md
├── agents/
│   └── verifier-agent.md                 # Trae Verifier 系统提示词
├── templates/
│   ├── task-with-boundary.md             # 通用任务模板
│   └── task-python-go.md                 # Python/Go 后端任务模板
├── examples/
│   ├── feature-map-example.md            # 通用 Feature Map
│   ├── feature-map-python-api.md         # Python API / 状态机
│   └── feature-map-go-service.md         # Go worker / 幂等与并发
├── skeletons/
│   ├── FEATURE-fastapi.md                # 可直接放进 FastAPI 仓库的骨架
│   └── FEATURE-go-internal.md            # Go internal/service 骨架
├── skills/
│   └── project-verification-skill.md     # 项目级 Launch→Evidence 验证技能
└── docs/
    └── success-cases.md                  # 成功案例与可复用做法
```

---

## 快速开始（Trae）

### 1. 创建 Verifier Agent

把 [`agents/verifier-agent.md`](agents/verifier-agent.md) 贴进自定义 Agent 系统提示词。

### 2. 用任务模板下发工作

- 后端优先：[`templates/task-python-go.md`](templates/task-python-go.md)
- Builder 完成后必须交 Verifier；你只看 `PASS` / `FAIL` / `BLOCKED` 与证据

### 3. 给易改坏模块写 FEATURE

| 你的项目 | 用这个骨架 |
|----------|------------|
| FastAPI 等 Python API | [`skeletons/FEATURE-fastapi.md`](skeletons/FEATURE-fastapi.md) |
| Go `internal/service` | [`skeletons/FEATURE-go-internal.md`](skeletons/FEATURE-go-internal.md) |
| 需要完整范例 | [`examples/feature-map-python-api.md`](examples/feature-map-python-api.md) / [`feature-map-go-service.md`](examples/feature-map-go-service.md) |

### 4. 仓库内放一份验证技能

复制 [`skills/project-verification-skill.md`](skills/project-verification-skill.md) 为项目的 `VERIFY.md`，改成真实的 health 与测试命令。任务里写：「按 VERIFY.md 执行并附输出」。

---

## 成功实践如何落到本仓库

| 成功做法 | 本仓库对应 |
|----------|------------|
| 验证优先（pstack） | Verifier + project-verification-skill |
| Feature Map | examples + skeletons |
| Tests-first / 验收可断言 | task-python-go 验收标准 + 验证命令 |
| 四层验证 | 静态/单测在验证命令里；Verifier 做评审层；curl/集成作运行态 |
| 证据包 | Verifier 输出格式；无证据 = BLOCKED |
| 先信任再并行 | docs/success-cases 中的采用顺序 |

更多说明：[docs/success-cases.md](docs/success-cases.md)

---

## Python / Go 特别注意

| 风险点 | 建议 |
|--------|------|
| 状态机被放宽 | FEATURE 写死合法迁移 + 测试 |
| 幂等失效 | 验收项写清「不重复流水/事件」 |
| 先读后写 | 条件更新；Go 可加 `-race` |
| 改坏契约 | 「不能破坏」写路径/字段/错误码 |
| 口头完成 | 无 pytest/go test 输出 → BLOCKED |

---

## 推荐工作流

```
你 → task-python-go + FEATURE 路径 + VERIFY.md
        ↓
     Builder
        ↓
     Verifier（证据关卡）
        ↓
你只处理 FAIL/BLOCKED，或抽查 PASS
```

---

## License

MIT
