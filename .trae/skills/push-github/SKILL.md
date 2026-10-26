---
name: push-github
description: 将当前项目 git commit 并 push 到 GitHub 仓库。当用户说提交到 GitHub、push 到 GitHub、发布项目、或要求 commit 并推送时使用。不要用于推送 Gitee 或其他 remote。
---

# Push GitHub（提交到 GitHub）

将当前项目安全地 commit 并 push 到 GitHub 仓库 `git@github.com:shenchang931/shenchang.git`。

## 前置检查

在执行任何 commit 或 push 之前，依次完成：

1. **确认当前在项目根目录** `d:\高级网页`。若不是，切换到该目录。
2. **检查 git 是否初始化**：`git rev-parse --is-inside-work-tree`。失败则先 `git init`。
3. **检查身份信息**：读取本仓库级别的 `git config user.name` 与 `git config user.email`。
   - 若 `user.email` 缺失或明显无效（如手机号、空值、`(none)`），先用仓库级 `git config user.email "shenchang931@users.noreply.github.com"` 修正；`user.name` 应为「沈畅」，否则修正。
   - 优先修改仓库级别（不加 `--global`），避免污染全局配置。
4. **检查 remote**：`git remote -v`。
   - 若尚未添加指向 GitHub 的 remote，执行：`git remote add github git@github.com:shenchang931/shenchang.git`
   - 若已存在同名 remote 但 URL 不对，执行：`git remote set-url github git@github.com:shenchang931/shenchang.git`
   - 保留现有 `origin`（指向 Gitee）不变，GitHub 使用独立 remote 名 `github`。
5. **检查 SSH 连通性**（可选，首次推送前执行）：`ssh -T git@github.com`。失败时提示用户检查 GitHub → Settings → SSH keys 是否已添加本机公钥 `C:\Users\沈畅\.ssh\id_ed25519.pub`。

## Commit 流程

注意：运行环境是 **Windows PowerShell**，禁止使用 bash heredoc（`<<'EOF'`）或 bash 重定向拼接提交信息。

1. 查看变更：`git status --short`。若无任何变更，告知用户「没有需要提交的内容」并停止。
2. 暂存全部变更：`git add -A`
3. 生成提交信息。优先顺序：
   - 若用户给出明确提交信息，直接使用
   - 否则读取 `git diff --cached --stat`，根据改动内容自动生成一行中文信息，格式：`更新：简述本次改动要点`
4. 执行 commit。PowerShell 兼容写法：
   - 短提交信息：`git commit -m "更新：XXX"`
   - 若含换行或特殊字符，先写入临时文件再提交：
     ```
     "提交信息第一行`n`n可选的详细说明" | Out-File -Encoding utf8 -FilePath .git\COMMIT_MSG_TEMP.txt -NoNewline
     git commit -F .git\COMMIT_MSG_TEMP.txt
     Remove-Item .git\COMMIT_MSG_TEMP.txt
     ```
5. 检查 commit 是否成功：`git log --oneline -1` 应显示新提交的哈希。

## Push 流程

1. 推送 GitHub：`git push github HEAD:main`（若当前分支不是 main，先 `git branch --show-current` 取实际分支名替换）
2. 推送成功后，向用户汇报：
   - commit 哈希前 7 位
   - 推送目标 remote 与分支
   - GitHub 仓库地址 `https://github.com/shenchang931/shenchang`
3. 若 `github` remote 尚未推送过（空仓首次推送），使用 `git push -u github HEAD:main` 设置 upstream。

## 约束

- 不执行 `git push --force`，除非用户明确要求
- 不删除或改写现有 Gitee `origin` remote
- 若 commit 失败（如 pre-commit hook 报错），先停止并向用户报告原因，不得使用 `--no-verify` 绕过
- push 失败时，先检查是网络/SSH/权限/远程拒绝中的哪一种，再给出对应修复建议
