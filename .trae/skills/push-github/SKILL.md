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

本仓库的既有推送约定（2026-10-26 已验证可用）：

- **GitHub 推送分支为 `github-clean`**，映射到远端 `master`：`git push github github-clean:master`
- 本地 `master-old` 分支是含 WorkBuddy 大文件（>100MB）的旧历史备份，**严禁推送到 GitHub**（会被 GH001 拒收）
- `.gitignore` 已排除 `WorkBuddy/`，新增提交不会再次引入大文件

1. 确认当前在 `github-clean` 分支上提交：`git branch --show-current`；若在其他分支，先切换到 `github-clean` 再提交。
2. 推送 GitHub：`git push github github-clean:master`
3. 推送成功后，向用户汇报：
   - commit 哈希前 7 位
   - 推送目标 remote 与分支
   - GitHub 仓库地址 `https://github.com/shenchang931/shenchang`
4. 日常增量推送直接 `git push github github-clean:master` 即可（upstream 已设置）。

## 约束

- 不执行 `git push --force`，除非用户明确要求
- 不删除或改写现有 Gitee `origin` remote
- 不改动本地分支结构（`master`/`master-old` 保留原样），只从 `github-clean` 推送
- 不重写 git 历史（`filter-branch`、`--orphan` 等已被安全策略限制，且无必要——干净历史已在 `github-clean`）
- SSH 必须使用系统 OpenSSH（仓库级 `git config core.sshCommand C:/Windows/System32/OpenSSH/ssh.exe`），Git 自带 ssh 会把 github.com 解析到 127.0.0.1 且不读用户 config；`~/.ssh/config` 已将 github.com 映射到 ssh.github.com:443（22 端口被封）
- 若 commit 失败（如 pre-commit hook 报错），先停止并向用户报告原因，不得使用 `--no-verify` 绕过
- push 失败时，先检查是网络/SSH/权限/远程拒绝中的哪一种，再给出对应修复建议
