# 仓库工作流与分支约定

本文件用于记录本仓库的分支用途与同步方式，避免后续遗忘。

## 分支角色

### `deploy`（我的自用分支 / 生产分支）

- 用途：承载我自己的定制改动（功能、配置、修复）。
- GitHub Actions：仓库默认分支设置为 `deploy`，定时任务与手动触发默认跑 `deploy`。
- 开发习惯：
  - 新功能从 `deploy` 切 `feature/*` 开发，完成后合并回 `deploy`。
  - 小改动也可以直接提交到 `deploy`（但更推荐走 `feature/*`，方便回滚与审查）。

### `main`（上游镜像分支）

- 用途：保持与上游仓库 `upstream/main` 一致，用于同步上游最新代码。
- 约定：不在 `main` 上开发自己的功能，不直接把个人改动提交到 `main`。

## 同步上游（把 upstream 最新带到我的代码里）

目标：把上游 `upstream/main` 的最新改动同步到我的 `deploy`，如有冲突由我手动解决。

推荐流程（merge 方式，最稳）：

1. 更新本地上游引用
   - `git fetch upstream`

2. 让本地 `main` 对齐上游
   - `git checkout main`
   - `git reset --hard upstream/main`
   - `git push --force-with-lease origin main`

3. 把上游更新合并到 `deploy`
   - `git checkout deploy`
   - `git merge main`
   - 如有冲突：手动解决 → `git add ...` → `git commit`
   - `git push origin deploy`

## 给上游提 PR（贡献回原仓库）

- 不要从 `deploy` 直接提 PR（会夹带我的私货）。
- 从 `main`（上游镜像）切分支提 PR：
  - `git checkout main`
  - `git checkout -b pr/<topic>`
  - 开发完成后推送到 fork，再向上游仓库发起 PR。

