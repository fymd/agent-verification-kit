# Failure Record 模板

复制到业务仓库：`docs/failures/YYYYMMDD-<短标题>.md`

---

```markdown
# Failure：<短标题>

- 日期：
- 任务 ID：
- 模块 / 路径：
- 严重度：P0 | P1 | P2
- 相关 Feature Map：

## 现象

## 错误做法（Agent 或流程做了什么）

## 根因
（一句话 + 证据）

## 正确做法

## 防再犯（至少完成一项，否则任务不得永久关闭）
- [ ] 更新 Feature Map：
- [ ] 新增或修复测试：
- [ ] 写入 Playbook / Skill 禁忌：
- [ ] 更新默认验收或回归清单：

## 防再犯如何验证
```bash
# 命令或检查说明
```

## 检索关键词
（主控开干前搜索用：模块名、错误码、现象词）
```
