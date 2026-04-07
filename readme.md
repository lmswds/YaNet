# Yet another Internet

基于 [dahaha-365/YaNet](https://github.com/dahaha-365/YaNet) 修改的自用规则，借助claude神力，在原版基础上添加了自定义直连进程、域名规则及地区正则优化。

## Mihomo Party 订阅链接

订阅 `personal` 分支（包含个人定制内容）：

```
https://raw.githubusercontent.com/lmswds/YaNet/personal/Mihomo/global_script.js
```

## 分支说明

| 分支 | 用途 |
|------|------|
| `main` | 跟踪上游原版，不做个人修改 |
| `personal` | 个人定制分支，Mihomo Party 订阅此分支 ✅ |

## 同步上游更新

已通过 **GitHub Actions** 实现自动同步（每季度首月 1 日北京时间 08:00 自动运行）。

### 自动同步流程

```
upstream/main ──自动──► origin/main ──自动──► origin/personal（若无冲突）
                                                      │
                                               有冲突时 Action 失败
                                               GitHub 自动发邮件通知 ⬇
```

1. Actions 每月自动拉取上游更新并合并到 `main`
2. 若合并到 `personal` **无冲突** → 自动推送，订阅内容自动更新 ✅
3. 若 **有冲突** → Action 失败，GitHub 自动发邮件通知，需手动处理

### 手动触发同步

在 [Actions 页面](../../actions/workflows/sync-upstream.yml) 点击 **Run workflow** 可立即触发。

### 冲突时手动处理

收到 Issue 通知后，在本地执行：

```bash
git fetch upstream
git checkout personal
git merge main
# 搜索 // 【个人 关键字，定位个人定制区域，保留 personal 版本
git add Mihomo/global_script.js
git commit -m "chore: merge upstream updates, keep personal customizations"
git push origin personal
```

> 详细冲突处理说明见 [`.agent/workflows/sync-upstream.md`](.agent/workflows/sync-upstream.md)