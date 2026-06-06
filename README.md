# gitee-cli

[Gitee](https://gitee.com) API v5 命令行工具，灵感来自 [`gh`](https://cli.github.com/)（GitHub CLI）。

简体中文 | [English](README_EN.md)

无需离开终端，管理 Issue、Pull Request、标签等。

## 快速开始

### 1. 获取个人访问令牌

Gitee → 设置 → 私人令牌 → 生成。勾选 `projects`、`issues`、`pull_requests` 权限。

### 2. 安装

**方式一（推荐）：加入 PATH**

```bash
# 1) 复制到系统 PATH
sudo cp gitee /usr/local/bin/
sudo chmod +x /usr/local/bin/gitee

# 2) 验证
gitee help
```

**方式二：source 到 Shell 配置**

如果你不想复制到系统目录，可以 source 到当前 shell：

```bash
echo "source $(pwd)/gitee" >> ~/.zshrc   # 或 ~/.bashrc
source ~/.zshrc                          # 立即生效
```

> ⚠️ **注意**：`source` 方式只在当前终端会话有效，新开终端需要重新 source 或写入配置文件。推荐使用方式一加入 PATH。

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
gitee repo                            # 查看仓库信息
gitee repo create <名称>              # 创建仓库 [--description] [--private]
gitee repo delete                     # 删除仓库
gitee repo fork                       # Fork 仓库
gitee repo star                       # Star 仓库
gitee repo unstar                     # Unstar 仓库
gitee repo watch                      # Watch 仓库
gitee repo unwatch                    # Unwatch 仓库
gitee repo list [page] [per_page]     # 列出授权仓库
gitee wiki list                       # 列出 Wiki 页面（需先在网页启用 Wiki）
gitee wiki get <slug>                 # 查看 Wiki 页面
gitee wiki create "标题" "正文"        # 创建/更新 Wiki 页面
gitee wiki delete <slug>              # 删除 Wiki 页面
gitee whoami                          # 查看当前登录用户
gitee assign <issue编号> <用户名>     # 指派 Issue
```

### 搜索与发现（公开内容，无需 token）

```bash
gitee search <关键词> [数量]          # 搜索仓库（默认 10 条，最多 50）
gitee user <用户名>                   # 查看用户资料
gitee trending [数量]                 # 热门仓库推荐（默认 20 条，最多 50）
gitee version                         # 显示版本号
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

| 变量 | 必填 | 默认值 | 说明 |
|------|------|--------|------|
| `GITEE_TOKEN` | 部分 | — | Issue/PR/Label 等操作**需要**；search/user/trending 等公开查询**不需要** |
| `GITEE_OWNER` | 否 | 从 git remote 推断 | 目标仓库所有者 |
| `GITEE_REPO` | 否 | 从 git remote 推断 | 目标仓库名 |
| `GITEE_API_BASE` | 否 | `https://gitee.com/api/v5` | API 基础地址 |

## 与 `gh` / `opencli` 对比

| 功能 | `gh` (GitHub) | `opencli gitee` | `gitee-cli` |
|------|---------------|-----------------|-------------|
| 认证 | `gh auth login` | ❌ 不需要 | `export GITEE_TOKEN=...` |
| Issue/PR/Label 增删改查 | 有 | ❌ 无 | ✅ 有 |
| 搜索仓库 | 有 | ✅ 有（爬公开页） | ✅ 有（API） |
| 用户资料 | 有 | ✅ 有 | ✅ 有 |
| Trending/推荐 | 有 | ✅ 有（浏览器渲染） | ✅ 有（搜索 API 近似） |
| Actions/CI | 有 | ❌ 无 | ❌ 无 |

`gitee-cli` 是**管理后台**（靠 token 操作私有仓库），`opencli gitee` 是**公开浏览器**（只能看公开内容）。两者互补，日常 Issue/PR 管理必须用 `gitee-cli`。

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
