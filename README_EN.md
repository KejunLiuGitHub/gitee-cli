# gitee-cli

A command-line tool for [Gitee](https://gitee.com) API v5, inspired by [`gh`](https://cli.github.com/) (GitHub CLI).

Manage Issues, Pull Requests, labels, and more — without leaving your terminal.

[简体中文](README.md) | English

## Quick Start

### 1. Get a Personal Access Token

Gitee → Settings → Private Token → Generate. Check `projects`, `issues`, `pull_requests` scopes.

### 2. Install

```bash
# Source into your shell config for permanent use:
echo "source /path/to/gitee" >> ~/.zshrc   # or ~/.bashrc
```

Or copy `gitee` to any directory in your PATH and make it executable:

```bash
sudo cp gitee /usr/local/bin/
sudo chmod +x /usr/local/bin/gitee
```

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

| Variable | Required | Default |
|----------|----------|---------|
| `GITEE_TOKEN` | Yes | — |
| `GITEE_OWNER` | No | Inferred from git remote |
| `GITEE_REPO` | No | Inferred from git remote |
| `GITEE_API_BASE` | No | `https://gitee.com/api/v5` |

## Comparison with `gh`

| Feature | `gh` (GitHub) | `gitee` |
|---------|---------------|---------|
| Authentication | `gh auth login` | `export GITEE_TOKEN=...` |
| Issue/PR/Repo CRUD | Yes | Yes |
| Actions/CI | Yes | No |
| Codespaces | Yes | No |
| Extensions | Yes | No |

`gitee` focuses on daily collaboration commands (Issue/PR/Repo management) and does not cover Gitee-specific features (GVP, Pages, Enterprise API).

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
