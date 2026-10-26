---
name: git-commit-push
description: 提交并推送当前项目到 Gitee 远程仓库。Use when the user asks to 提交项目, git commit, push代码, 推送到gitee, 提交到远程仓库, or push to gitee. This skill runs the full git commit + push workflow targeting https://gitee.com/hen-chang/shenchang2026.git.
---

# 提交项目到 Gitee

将当前项目所有变更提交并推送到 Gitee 远程仓库 `https://gitee.com/hen-chang/shenchang2026.git`。

## 执行步骤

按以下顺序执行，每一步成功后再继续下一步：

### 1. 检查当前状态

```powershell
git status
git log --oneline -5
```

### 2. 暂存所有变更

```powershell
git add -A
```

### 3. 提交

根据 `git status` 中的变更内容，生成一条简洁的中文提交信息，然后执行：

```powershell
git commit -m "<提交信息>"
```

提交信息要求：
- 中文描述，简洁明了
- 概括本次变更的主要内容（如：新增练习页面、修复样式等）

### 4. 检查远程仓库配置

```powershell
git remote -v
```

- 若输出中包含 `https://gitee.com/hen-chang/shenchang2026.git`，直接进入第 5 步
- 若无远程或远程地址不符，执行：
  ```powershell
  git remote add origin https://gitee.com/hen-chang/shenchang2026.git
  ```
  （若 origin 已存在但地址错误，先执行 `git remote remove origin` 再添加）

### 5. 推送到远程

```powershell
git push -u origin master
```

- 若 `master` 分支不存在，改用当前分支名：
  ```powershell
  git branch --show-current
  ```
- 若远程已有历史且推送被拒绝（non-fast-forward），不要强制推送，先向用户报告情况

### 6. 报告结果

推送成功后，告知用户：
- 提交信息
- 推送的分支
- 远程仓库地址

若任何一步失败，停止并报告完整错误信息，不要自行尝试危险操作（如 `push --force`）。
