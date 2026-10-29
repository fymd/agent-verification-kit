# 项目级验证技能模板（Python / Go 服务）

对标成功实践里的 **verification skill**：让 Agent 知道如何在本仓库**启动、自检、驱动、留证、清理**。  
复制到你的项目（如 `docs/agent/VERIFY.md` 或 Trae/WorkBuddy 的 Skill），按实际命令改写。

---

## 技能正文（可粘贴）

```markdown
# 本项目验证技能

你在修改本仓库后，必须按本节证明工作有效。没有证据不得声称完成。

## 1. Launch（如何认为服务「就绪」）

### Python（示例）
```bash
# 安装与启动按项目改写
pip install -e ".[dev]"   # 或 poetry/uv
uvicorn app.main:app --port 8000
# 就绪标准：curl -sf http://127.0.0.1:8000/health 返回 200
```

### Go（示例）
```bash
go run ./cmd/api
# 或：make run
# 就绪标准：curl -sf http://127.0.0.1:8080/healthz 返回 200
```

## 2. Doctor（只读，确认值得继续测）

- 健康检查通过
- 配置/迁移版本符合本地预期（若有）
- 不连生产；只用 local/docker-compose 依赖

失败 → 停止并报告 BLOCKED（环境问题），不要改业务代码「绕过」。

## 3. Drive（如何操作本服务）

- 优先跑自动化测试（最快、可重复）
- 需要行为证明时用 curl/grpcurl，步骤写进任务的验收标准
- 有 Feature Map 的模块：按 Map 中的入口与回归清单操作

### 默认测试命令

Python:
```bash
pytest -q
# 或收窄：pytest tests/services/test_order_service.py -q
```

Go:
```bash
go test ./... -count=1
# 模块级：go test ./internal/service/ -count=1
# 并发相关：go test ./internal/service/ -race -count=1
```

## 4. Evidence（必须保留的证据）

完成报告中至少包含：

1. 执行的命令原文
2. 退出码
3. 失败时的关键错误行；成功时一行摘要即可
4. 若做了手动 API 调用：请求与响应关键字段
5. 对照验收标准的逐条结果

不要用「测试应该会过」代替输出。

## 5. Cleanup

- 只停止本次启动的进程
- 不要删除测试输出或证据
- 不要清掉别人的 docker 卷（除非任务明确要求且可恢复）

## 6. 判定约定

- 所有验收标准有证据 → 可交给 Verifier 申请 PASS
- 任一标准无证据或失败 → FAIL，说明如何复现
- 环境/权限不足 → BLOCKED，列出缺失项
```

---

## 接入建议

1. 每个服务仓库维护一份 `VERIFY.md`（本模板填空）。
2. 任务模板「验证方式」直接引用：`按 docs/agent/VERIFY.md 执行并附输出`。
3. Verifier 只检查证据是否齐全、是否与验收标准对应。
4. 随健康检查路径、默认测试命令变化而更新本文（可对标 pstack 的 maintain-verification-skill 习惯）。
