# gitee-cli

[Gitee](https://gitee.com) API v5 命令行工具，灵感来自 [`gh`](https://cli.github.com/)（GitHub CLI）。

无需离开终端，管理 Issue、Pull Request、标签等。

## 快速开始

### 1. 获取个人访问令牌

Gitee → 设置 → 私人令牌 → 生成。勾选 `projects`、`issues`、`pull_requests` 权限。

### 2. 安装

```bash
# 加入 Shell 配置以永久使用：
echo "source /path/to/gitee" >> ~/.zshrc   # 或 ~/.bashrc
```

或复制 `gitee` 到 PATH 中的任意位置并添加执行权限：

```bash
sudo cp gitee /usr/local/bin/
sudo chmod +x /usr/local/bin/gitee
```

### 3. 认证

```bash
export GITEE_TOKEN="你的令牌"
```

### 4. 使用

工具会自动从 git remote 推断仓库（优先 `gitee` remote，其次 `origin`）：

```bash
git remote add gitee https://gitee.com/<用户名>/<仓库名>.git
```

或手动指定：

```bash
export GITEE_OWNER="你的命名空间"
export GITEE_REPO="你的仓库"
```

## 命令参考

### Issue

```bash
gitee issue list [状态]              # 列出 Issue（默认: open）
gitee issue create "标题" "正文"     # 创建 Issue（可选标签）
gitee issue view <编号>             # 查看 Issue 详情
gitee issue close <编号>            # 关闭 Issue
gitee issue reopen <编号>           # 重新打开 Issue
gitee issue comment <编号> "正文"   # 添加评论
gitee issue edit <编号>             # 编辑 Issue
      [--title] [--body] [--labels]
```

### Pull Request

```bash
gitee pr list [状态]                                       # 列出 PR
gitee pr create "标题" "正文" --head 分支 [--base main]    # 创建 PR
gitee pr view <编号>                                       # 查看 PR 详情
gitee pr merge <编号>                                      # 合并 PR
gitee pr close <编号>                                      # 关闭 PR
gitee pr edit <编号> [--title] [--body]                    # 编辑 PR
gitee pr checkout <编号>                                   # 检出 PR 分支
```

### 标签

```bash
gitee label list                      # 列出标签
gitee label create <名称> <颜色>      # 创建标签（颜色为 6 位 hex）
gitee label delete <名称>             # 删除标签
```

### 仓库

```bash
gitee repo          # 查看仓库信息
gitee whoami        # 查看当前登录用户
gitee assign <issue编号> <用户名>   # 指派 Issue
```

## 测试与诊断

仓库附带 `./gitee-test` 诊断脚本，方便 AI Agent 或新用户快速验证环境：

```bash
./gitee-test              # 终端输出摘要 + 生成 gitee-test-report.md
./gitee-test my-report.md # 指定报告文件名
```

检查项包括：
- **环境依赖**：bash、curl、python3 (≥3.6)、git
- **配置**：GITEE_TOKEN、git remote、owner/repo 推断
- **API 冒烟**：whoami、repo、issue list、label list、pr list、401 认证失败
- **脚本健壮性**：语法检查、可选 shellcheck 静态分析

报告为 Markdown 格式，AI 可直接读取定位问题，无需反复询问用户环境信息。

## 工作原理

`gitee` 是一个 shell 脚本，封装 [Gitee OpenAPI v5](https://gitee.com/api/v5/swagger) REST 接口：

- `curl` — HTTP 请求
- `python3` — JSON 格式化
- `git remote` — 自动推断当前仓库

令牌通过 `Authorization` 请求头发送，**不出现**在 URL 或请求体中。所有请求均校验 HTTP 状态码。

## 环境变量

| 变量 | 必填 | 默认值 |
|------|------|--------|
| `GITEE_TOKEN` | 是 | — |
| `GITEE_OWNER` | 否 | 从 git remote 推断 |
| `GITEE_REPO` | 否 | 从 git remote 推断 |
| `GITEE_API_BASE` | 否 | `https://gitee.com/api/v5` |

## 与 `gh` 对比

| 功能 | `gh` (GitHub) | `gitee` |
|------|---------------|---------|
| 认证 | `gh auth login` | `export GITEE_TOKEN=...` |
| Issue/PR/Repo 增删改查 | 有 | 有 |
| Actions/CI | 有 | 无 |
| Codespaces | 有 | 无 |
| 扩展 | 有 | 无 |

`gitee` 聚焦日常协作命令（Issue/PR/Repo 管理），不涉及 Gitee 特有功能（GVP、Pages、企业版 API）。

## 注意事项

- **Gitee Issue 创建接口与 GitHub 不同**：端点为 `POST /v5/repos/{owner}/issues`，仓库名放在请求体中，不在 URL 路径中。这与 GitHub 的 `POST /repos/{owner}/{repo}/issues` 不同。
- 令牌通过 `Authorization` 请求头传递，不出现在 URL 或请求体中。
- 脚本调用 `python3` 做 JSON 格式化。如果环境没有 `python3`，会输出原始 JSON。
- **Windows 用户**：
  1. **Git Bash（推荐）**：安装 Git for Windows，右键项目文件夹选择 "Git Bash Here"，配置好环境变量后即可正常使用。
  2. **WSL**：将代码复制到 WSL 目录中运行，体验与 Linux 一致。
  3. 如果 `python3` 命令不存在（如 Cygwin 或 MSYS2），将脚本中的 `python3` 替换为 `python`。

## License

MIT — 见 [LICENSE](LICENSE)。
