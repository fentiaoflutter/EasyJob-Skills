---
name: dev-feature-branch-rebase
description: 从最新远端集成分支（`dev_v*`）创建功能分支（不设 push upstream），或把当前功能分支 rebase 到其源 `dev_v*`，保持工作区干净并谨慎处理冲突 / force-push。Use when the user asks to 创建需求分支 / 新分支 / PR分支 / 从 dev 拉分支 / 更新代码 / 同步源分支 / rebase 到最新 dev.
---

# dev 功能分支创建与更新（rebase）

> 一句话目标：标准化两个 git 操作 —— 从最新 `dev_v*` 创建功能分支、把当前分支 rebase 到源 `dev_v*`，避免选错 baseline 或污染工作区。

## 适用仓库

任意以 `dev_v<version>` 命名集成分支的 Git 仓库（Git 远端名默认 `origin`，可替换）。

## 触发场景

| 用户意图 | 典型说法 | 进入 |
|----------|----------|------|
| 创建需求分支 | 「拉新分支」「从 dev 拉一个 PR 分支」「新建 PR-xxx 分支」 | 流程 A |
| 更新当前分支 | 「更新代码」「同步源分支」「rebase 到最新 dev」 | 流程 B |

---

## 流程 A：创建新需求分支

### 约定

- **baseline** = 提交时间最新的 `refs/remotes/origin/dev_v*`（**不要写死版本号**）。
- **功能分支命名**：`dev/feature/pr_<编号>`（小写 `pr_`），如 `dev/feature/pr_XXXXX`。
- **不设 push upstream**：避免与"首次 push 该功能分支"混淆；若 Git 自动设了，执行 `git branch --unset-upstream`。
- **尽量不打扰当前工作区**：用户工作区脏时，**优先用「只移动 ref、不 checkout」** 的方式建分支。

### 步骤

```bash
# 1. 拉最新远端
git fetch origin

# 2. 解析最新 baseline
BASE=$(git for-each-ref 'refs/remotes/origin/dev_v*' \
  --sort=-committerdate --format='%(refname:short)' | head -1)
# BASE 形如 origin/dev_vX.Y.Z；若为空，排查远端命名

# 3. 建分支（二选一）
# A1. 不切换（推荐）：
git branch dev/feature/pr_<编号> "$BASE"
git branch --unset-upstream dev/feature/pr_<编号> 2>/dev/null || true

# A2. 需要同时切过去：
git checkout -b dev/feature/pr_<编号> "$BASE"
git branch --unset-upstream dev/feature/pr_<编号> 2>/dev/null || true
```

### 回报

- 新分支名、`BASE` 全名、该点的 `git log -1 --oneline`。
- 若未 checkout，提醒：`git checkout dev/feature/pr_<编号>`。

---

## 流程 B：更新当前功能分支（rebase）

### 前置

- 必须已 checkout 到要更新的功能分支。
- 工作区有未提交改动时，**先提醒 stash / commit**，不静默丢改。

### 找源分支

源分支 = 该功能分支当初基于的 `origin/dev_v*`。

- 若对话/上下文已明确（如 PR-XXXXX 基于 `dev_vX.Y.Z`），直接用。
- 不明确时：
  - 优先**问用户**「源分支是哪一个 `dev_v*`」；或
  - 用 `git reflog show <当前分支> --date=short | tail -20` 辅助推断。

### 步骤

```bash
git fetch origin
git status -sb            # 确认在目标功能分支且无意外脏改
git rebase origin/dev_vX.Y.Z
```

### 冲突 / force-push

- rebase 停止时：列出冲突文件与 `git status`，说明用户需本地解决后 `git rebase --continue`，或 `git rebase --abort`；**不在未读冲突文件前提下猜测合并结果**。
- 已 push 过的功能分支：rebase 会改写历史；提醒可能需要 `git push --force-with-lease`，并确认无人基于旧历史开发。

---

## 与 merge 的切换

若用户明确说改用 **merge** 更新：把上面的 `git rebase` 换成 `git merge origin/dev_vX.Y.Z` 即可，其余流程不变。

## 协作守则

- 冲突解决、是否 abort、是否 force-push：**先在对话里说明并等用户回复**，再继续执行。

## 示例

> **用户**：帮我从 dev 拉个 PR-12345 分支，但别动我现在工作区。
> **Skill**：`git fetch` → 解析 `BASE=origin/dev_vX.Y.Z` → `git branch dev/feature/pr_12345 "$BASE"` → `--unset-upstream` → 回报新分支名 + baseline + "未 checkout，开发前请 `git checkout dev/feature/pr_12345`"。
