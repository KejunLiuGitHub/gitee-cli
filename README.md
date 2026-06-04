# gitee-cli

Command-line interface for [Gitee](https://gitee.com) API v5, inspired by [`gh`](https://cli.github.com/) (the GitHub CLI).

Manage issues, pull requests, labels, and more — without leaving the terminal.

## Quick start

### 1. Get a personal access token

Gitee → Settings → Private Token → generate.
The token needs `projects`, `issues`, `pull_requests` scopes.

### 2. Install

```bash
# Source in your shell config for permanent use:
echo "source /path/to/gitee" >> ~/.zshrc   # or ~/.bashrc
```

Or copy `gitee` anywhere on your PATH and make it executable:
```bash
sudo cp gitee /usr/local/bin/
sudo chmod +x /usr/local/bin/gitee
```

### 3. Authenticate

```bash
export GITEE_TOKEN="your-token"
```

### 4. Use it

The tool infers the repository from your git remote (`gitee` remote first, then `origin`):

```bash
git remote add gitee https://gitee.com/<owner>/<repo>.git
```

Or set explicitly:
```bash
export GITEE_OWNER="your-namespace"
export GITEE_REPO="your-repo"
```

## Commands

### Issues

```bash
gitee issue list [state]              # List issues (default: open)
gitee issue create "title" "body"     # Create issue (labels optional)
gitee issue view <number>             # Show issue details
gitee issue close <number>            # Close issue
gitee issue reopen <number>           # Reopen issue
gitee issue comment <number> "body"   # Add comment
```

### Pull Requests

```bash
gitee pr list [state]                                       # List PRs
gitee pr create "title" "body" --head branch [--base main]  # Create PR
gitee pr view <number>                                      # Show PR details
gitee pr merge <number>                                     # Merge PR
```

### Repository

```bash
gitee repo          # Show repo info (name, stars, forks, etc.)
gitee labels        # List labels with colors
gitee whoami        # Show authenticated user
gitee assign <issue-num> <username>   # Assign issue to user
```

## How it works

`gitee` is a shell script that wraps the [Gitee OpenAPI v5](https://gitee.com/api/v5/swagger) REST endpoints. It uses:
- `curl` for HTTP requests
- `python3` for JSON pretty-printing
- `git remote` to infer the current repository

The token is passed via `access_token` in the request body (POST/PATCH) or query string (GET), consistent with Gitee API v5 conventions.

## Environment variables

| Variable | Required | Default |
|----------|----------|---------|
| `GITEE_TOKEN` | Yes | — |
| `GITEE_OWNER` | No | Inferred from git remote |
| `GITEE_REPO` | No | Inferred from git remote |
| `GITEE_API_BASE` | No | `https://gitee.com/api/v5` |

## Comparison with `gh`

| Feature | `gh` (GitHub) | `gitee` |
|---------|---------------|---------|
| Auth | `gh auth login` | `export GITEE_TOKEN=...` |
| Issue/PR/Repo CRUD | Yes | Yes |
| Actions/CI | Yes | No |
| Codespaces | Yes | No |
| Extensions | Yes | No |

`gitee` covers issue/PR/repo management — the day-to-day collaboration commands. It does not wrap Gitee-specific features like GVP, Pages, or Enterprise API.

## Notes

- **Gitee issue creation API is different from GitHub**: The endpoint is `POST /v5/repos/{owner}/issues` and the `repo` name goes in the request body — not in the URL path. This is a key difference from GitHub's `POST /repos/{owner}/{repo}/issues`.
- All POST/PATCH requests send the token in the request body as `access_token`. GET requests pass it as a query parameter.
- The tool defaults to reading from `python3` for JSON formatting. If `python3` is not available, raw JSON is printed instead.

## License

MIT — see [LICENSE](LICENSE).
