# 完整任务卡（一次做对版）

主控建卡、专家交接统一用此模板。进入 Doing 前记忆门与 Spec 门须可勾选。

---

```markdown
## 任务卡

| 字段 | 内容 |
|------|------|
| ID | T- |
| 标题 | |
| 负责人 | Spec / Backend / Debug / Verifier |
| 状态 | Backlog / Doing / Verify / Done / Blocked |
| 优先级 | P0 / P1 / P2 |

### 目标
（一句话）

### 验收标准（可判定）
1. 
2. 

### 不能破坏
- 

### 范围
- 可以改：
- 不要动：

### 验证命令
```bash

```

### Spec 门（全部是才可 Doing）
- [ ] 目标清晰
- [ ] 验收标准可判定
- [ ] 不能破坏已列或写明无回归面
- [ ] 验证命令/步骤已写
- [ ] 范围已写
- [ ] 待确认已清空或用户已确认

### 记忆门（全部是才可 Doing）
- [ ] 已检索 docs/failures（关键词：____）；命中：无 / 见下
- [ ] 已读 Feature Map：路径 ____ 或 无
- [ ] 已引用 Playbook：路径 ____ 或 无

### 引用
- Feature Map：
- Failure Record：
- Playbook：

### 依赖
- 

### 交付（负责人）
- [ ] 改动说明
- [ ] 验证输出
- [ ] 验收标准自检

### 验收（Verifier）
- 判定：PASS / FAIL / BLOCKED
- 摘要：

### 纠错（若曾 FAIL）
- Failure Record 路径：
- 防再犯已落地：是 / 否（否不得 Done）

### 成功沉淀（PASS 后）
- [ ] 无需 Playbook
- [ ] 已写/更新 Playbook：路径 ____
```
