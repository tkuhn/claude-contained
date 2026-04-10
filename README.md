# claude-contained

Run [Claude Code](https://docs.anthropic.com/en/docs/claude-code) in a sandboxed Docker container. Your project directory is mounted into the container; everything else on your host is isolated.

## Quick start

```bash
# Interactive session in the current directory
./claude-contained

# One-shot task
./claude-contained "fix the failing tests"

# Build/rebuild the image explicitly
./claude-contained --build

# Update pinned dependencies (base image + Claude Code version)
./claude-contained --update
```

## Authentication

Set one of these environment variables (or put them in `~/.config/claude-contained.env`):

| Variable | Description |
|----------|-------------|
| `CLAUDE_CODE_OAUTH_TOKEN` | OAuth token from a Max/Pro subscription (preferred) |
| `ANTHROPIC_API_KEY` | API key for pay-per-token billing |

Get your OAuth token:

```bash
claude login && claude setup-token
```

## Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `CLAUDE_CONTAINED_IMAGE` | `claude-contained` | Docker image name |
| `CLAUDE_CONTAINED_MODEL` | *(CC default)* | Model override |
| `CLAUDE_CONTAINED_NETWORK` | `default` | `default` for normal networking, `none` for full isolation |

## Security

The container runs with:

- **Non-root user** inside the container
- **Resource limits** (`--memory 4g`, `--pids-limit 256`)
- **Pinned dependencies** (base image digest + Claude Code version)
- **Auth tokens passed via env-file**, not CLI args (avoids leaking into `ps`/`/proc`)
- **Directory guard** preventing accidental mount of `$HOME` or `/`
- **No `--privileged`**, no Docker socket mount
- **`--dangerously-skip-permissions`** is used inside the container since the sandbox itself provides the safety boundary

For sensitive work, set `CLAUDE_CONTAINED_NETWORK=none` to fully air-gap the container.
