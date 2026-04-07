---
description: 同步上游 YaNet 仓库更新，同时保留个人定制内容（分支工作流）
---

# 前置说明

本工作流的核心思路：

- `main` 分支：**只跟踪上游**，不做个人修改
- `personal` 分支：**个人定制分支**，Mihomo Party 订阅此分支的 raw URL
- 每次上游更新后，将上游变更 merge 到 `personal`，仅在冲突时手动处理个人定制区域

个人定制内容均标记有 `// 【个人` 注释，合并冲突时以此为检索关键字。

---

# 第一步：初始化（仅执行一次）

## 1.1 添加上游远程仓库

```bash
git remote add upstream https://github.com/dahaha-365/YaNet.git
```

验证是否添加成功：

```bash
git remote -v
```

应看到：
```
origin    https://github.com/lmswds/YaNet.git (fetch)
origin    https://github.com/lmswds/YaNet.git (push)
upstream  https://github.com/dahaha-365/YaNet.git (fetch)
upstream  https://github.com/dahaha-365/YaNet.git (push)
```

## 1.2 创建 personal 个人定制分支

```bash
git checkout -b personal
git push origin personal
```

## 1.3 更新 Mihomo Party 订阅 URL

将 Mihomo Party 中脚本的订阅 URL 从 `main` 改为 `personal` 分支的 raw 地址：

```
https://raw.githubusercontent.com/lmswds/YaNet/personal/Mihomo/global_script.js
```

> ⚠️ 初始化完成后，日常只需执行下面的「同步上游」步骤。

---

# 第二步：同步上游（日常更新）

## 2.1 拉取上游最新内容

```bash
git fetch upstream
```

## 2.2 将上游更新合并到 main 分支

```bash
git checkout main
git merge upstream/main
git push origin main
```

## 2.3 将 main 合并到 personal 分支

```bash
git checkout personal
git merge main
```

**若无冲突**，直接推送：

```bash
git push origin personal
```

**若有冲突**，进入下一步。

---

# 第三步：处理合并冲突（按需执行）

## 3.1 查看哪些文件冲突

```bash
git status
```

## 3.2 在冲突文件中定位个人定制区域

在 `Mihomo/global_script.js` 中搜索以下关键字，快速定位个人定制区域：

```
// 【个人
```

冲突块格式如下：
```
<<<<<<< HEAD
// 你的个人版本（personal 分支）
=======
// 上游的新版本（来自 main）
>>>>>>> main
```

## 3.3 解决冲突原则

| 冲突区域 | 保留策略 |
|----------|----------|
| `const CUSTOM_RULES_URL` | ✅ 保留你的（personal 版本） |
| `allRegionDefinitions` 中的正则修改 | ✅ 保留你的（personal 版本） |
| `rules` 数组中的 `PROCESS-NAME-REGEX` / `DOMAIN-SUFFIX` 个人规则 | ✅ 保留你的（personal 版本） |
| `args` 默认参数（`ruleSet`、`regionSet` 等）的个人设置 | ✅ 保留你的（personal 版本） |
| 上游新增的功能逻辑、新节点、bugfix | ✅ 保留上游版本（main 版本） |

## 3.4 标记冲突已解决并提交

```bash
git add Mihomo/global_script.js
git commit -m "chore: merge upstream updates, keep personal customizations"
git push origin personal
```

---

# 附录：个人定制区域速查

以下是当前 `global_script.js` 中所有个人定制点，合并冲突时重点检查：

| 行号（参考）| 变量 / 区域 | 说明 |
|------------|------------|------|
| ~12 | `CUSTOM_RULES_URL` | 指向自己 fork 的 custom_rules.list |
| ~47 | `args.ruleSet` | 按需启用的规则集（如 `openai;ads;google;games;japan`） |
| ~169–203 | `rules` 数组前半段 | 自定义直连进程规则、域名直连规则 |
| ~205–272 | `allRegionDefinitions` | 正则顺序调整（中文优先）、TK 土耳其代码修正 |

---

# 快速参考命令

```bash
# 日常同步（无冲突时的完整流程）
git fetch upstream
git checkout main && git merge upstream/main && git push origin main
git checkout personal && git merge main && git push origin personal
```
