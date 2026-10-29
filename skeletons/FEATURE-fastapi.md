# FEATURE 骨架（FastAPI）

复制到你的仓库，例如：`docs/features/<name>.md` 或 `app/<module>/FEATURE.md`。  
填空后，在任务模板里写上路径，要求 Agent：**先读本文，再改代码**。

---

```markdown
# <功能名>

## 目的
<一句话>

## 入口
- HTTP：`<METHOD> <path>`
- 路由文件：`app/api/...`
- 服务：`app/services/...`
- 模型/仓储：`app/models/...` / `app/repositories/...`

## 核心行为
1. 
2. 
3. 

## 合法状态 / 错误（如适用）
- 允许的迁移或输入：
- 必须拒绝的情况与错误码：
- 幂等约定：

## 边界
### 可以改
- 

### 绝对不能动
- 鉴权中间件 / 其他聚合 / 迁移脚本（按需勾选并写具体路径）
- 

### 隐式约定
- 

## 回归清单
- [ ] 主路径成功
- [ ] 非法输入/非法迁移被拒绝
- [ ] 重复请求幂等（如适用）
- [ ] 相关 pytest 通过

## 验证命令
```bash
pytest tests/api/test_<name>.py -q
# 或
pytest tests/ -k "<keyword>" -q
```

## 常见失败模式
- 现象 → 原因 → 应避免

## 维护
路由、错误码、状态枚举变更时同步更新本文与测试。
```
