# gitee-cli

A command-line tool for [Gitee](https://gitee.com) API v5, inspired by [`gh`](https://cli.github.com/) (GitHub CLI).

Manage Issues, Pull Requests, labels, and more — without leaving your terminal.

[简体中文](README.md) | English

## Quick Start

### 1. Get a Personal Access Token

Gitee → Settings → Private Token → Generate. Check `projects`, `issues`, `pull_requests` scopes.

### 2. Install

**Option 1 (recommended): Add to PATH**

```bash
# 1) Copy to a system PATH directory
sudo cp gitee /usr/local/bin/
sudo chmod +x /usr/local/bin/gitee

# 2) Verify
gitee help
```

**Option 2: Source into shell config**

If you prefer not to copy to a system directory:

```bash
echo "source $(pwd)/gitee" >> ~/.zshrc   # or ~/.bashrc
source ~/.zshrc                          # apply immediately
```

> ⚠️ **Note**: The `source` approach only works for the current terminal session unless you write it to your shell config. Option 1 (PATH) is recommended.

### 3. Authenticate

```bash
export GITEE_TOKEN="your-token"
```

### 4. Usage

The tool automatically infers the repository from git remotes (prefers `gitee`, falls back to `origin`):

```bash
git remote add gitee https://gitee.com/<username>/<repo>.git
```

Or specify manually:

```bash
export GITEE_OWNER="your-namespace"
export GITEE_REPO="your-repo"
```

## Command Reference

### Issue

```bash
gitee issue list [state]              # List issues (default: open)
gitee issue create "title" "body"     # Create issue (optional labels)
gitee issue view <number>             # View issue details
gitee issue close <number>            # Close issue
gitee issue reopen <number>           # Reopen issue
gitee issue comment <number> "body"   # Add comment
gitee issue edit <number>             # Edit issue
      [--title] [--body] [--labels]
```

### Pull Request

```bash
gitee pr list [state]                                       # List PRs
gitee pr create "title" "body" --head branch [--base main]  # Create PR
gitee pr view <number>                                       # View PR details
gitee pr merge <number>                                      # Merge PR
gitee pr close <number>                                      # Close PR
gitee pr edit <number> [--title] [--body]                    # Edit PR
gitee pr checkout <number>                                   # Checkout PR branch
```

### Labels

```bash
gitee label list                      # List labels
gitee label create <name> <color>     # Create label (6-digit hex color)
gitee label delete <name>             # Delete label
```

### Repository

```bash
gitee repo          # Show repo info
gitee whoami        # Show current user
gitee assign <issue-number> <username>   # Assign issue
```

### Search & Discovery (public, no token required)

```bash
gitee search <keyword> [limit]          # Search repositories (default 10, max 50)
gitee user <username>                   # Show user profile
gitee trending [limit]                  # Trending repositories (default 20, max 50)
gitee version                           # Show version
```

## Testing & Diagnostics

The repo includes `./gitee-test`, a self-contained diagnostic script for AI agents and new users:

```bash
./gitee-test              # Terminal summary + generates gitee-test-report.md
./gitee-test my-report.md # Custom report filename
```

Checks cover:
- **Environment**: bash, curl, python3 (≥3.6), git
- **Configuration**: GITEE_TOKEN, git remote, owner/repo inference
- **API smoke tests**: whoami, repo, issue list, label list, pr list, 401 auth failure
- **Script health**: syntax check, optional shellcheck lint

The report is Markdown-formatted so AI agents can read and diagnose issues directly.

## How It Works

`gitee` is a single shell script wrapping the [Gitee OpenAPI v5](https://gitee.com/api/v5/swagger) REST interface:

- `curl` — HTTP requests
- `python3` — JSON formatting
- `git remote` — Auto-detect current repository

The token is sent via the `Authorization` header and **never** appears in URLs or request bodies. All requests validate HTTP status codes.

## Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `GITEE_TOKEN` | Partial | — | **Required** for Issue/PR/Label operations; **not required** for public queries (search/user/trending) |
| `GITEE_OWNER` | No | Inferred from git remote | Target repo owner |
| `GITEE_REPO` | No | Inferred from git remote | Target repo name |
| `GITEE_API_BASE` | No | `https://gitee.com/api/v5` | API base URL |

## Comparison with `gh` / `opencli`

| Feature | `gh` (GitHub) | `opencli gitee` | `gitee-cli` |
|---------|---------------|-----------------|-------------|
| Authentication | `gh auth login` | ❌ Not needed | `export GITEE_TOKEN=...` |
| Issue/PR/Label CRUD | Yes | ❌ No | ✅ Yes |
| Search repositories | Yes | ✅ Yes (scrapes public pages) | ✅ Yes (API) |
| User profile | Yes | ✅ Yes | ✅ Yes |
| Trending/Recommended | Yes | ✅ Yes (browser-rendered) | ✅ Yes (search API approximation) |
| Actions/CI | Yes | ❌ No | ❌ No |

`gitee-cli` is a **management console** (token-based, operates on private repos), while `opencli gitee` is a **public browser** (can only view public content). They complement each other — daily Issue/PR management requires `gitee-cli`.

## Notes

- **Gitee Issue creation differs from GitHub**: The endpoint is `POST /v5/repos/{owner}/issues`, and the repo name goes in the request body, not the URL path. This is different from GitHub's `POST /repos/{owner}/{repo}/issues`.
- The token is passed via the `Authorization` header and never appears in URLs or request bodies.
- The script uses `python3` for JSON formatting. If `python3` is unavailable, raw JSON is output.
- **Windows users**:
  1. **Git Bash (recommended)**: Install Git for Windows, right-click the project folder and choose "Git Bash Here", then set environment variables and use normally.
  2. **WSL**: Copy the code into a WSL directory for a Linux-like experience.
  3. If `python3` is not available (e.g. Cygwin or MSYS2), replace `python3` with `python` in the script.

## License

MIT — see [LICENSE](LICENSE).
