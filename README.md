# ddev-claude-code

A DDEV addon that integrates [Claude Code](https://claude.ai/code) CLI into your DDEV environment.

## What It Does

- Installs Claude Code CLI, GitHub CLI (`gh`), and [ccusage](https://github.com/ryoppippi/ccusage) in the DDEV web container
- Mounts your host `~/.claude` and `~/.config/gh` directories into the container
- Injects `GH_TOKEN` and `CLAUDE_CODE_OAUTH_TOKEN` via a pre-start hook
- Provides `ddev claude` and `ddev claude-auto` commands

## Installation

```bash
ddev add-on get jamespublishlab/ddev-claude-code
ddev restart
```

### Prerequisites

- DDEV >= v1.23.0
- Claude Code installed on your host (`npm install -g @anthropic-ai/claude-code` or via `claude.ai/install.sh`)
- Run `claude` on your host at least once to initialize `~/.claude`
- GitHub CLI (`gh`) authenticated on your host (`gh auth login`)

## Usage

```bash
# Run Claude Code interactively
ddev claude

# Run with arguments
ddev claude --help
ddev claude --version

# Run in autonomous mode (skips permission prompts)
ddev claude-auto "add unit tests for the auth module"
```

### Checking the setup

```bash
ddev claude --version         # Claude Code CLI version
ddev exec gh --version        # GitHub CLI version
ddev exec 'echo $GH_TOKEN'   # Confirm token injection
```

## How It Works

1. **Pre-start hook** (`config.claude-code.yaml`): Captures your host's `gh auth token` and Claude OAuth token into `.ddev/.env.tokens`
2. **Docker Compose overlay** (`docker-compose.claude-code.yaml`): Mounts `~/.claude` and `~/.config/gh` into the container and loads `.env.tokens`
3. **Dockerfile** (`web-build/Dockerfile.claude-code`): Installs `gh`, `claude`, and `ccusage` in the web container

## Files

| File | Type | Purpose |
|------|------|---------|
| `docker-compose.claude-code.yaml` | Project | Volume mounts + env_file |
| `config.claude-code.yaml` | Project | Pre-start hook for token capture |
| `web-build/Dockerfile.claude-code` | Project | Installs CLI tools |
| `commands/web/claude` | Global | `ddev claude` command |
| `commands/web/claude-auto` | Global | `ddev claude-auto` command |

## Removal

```bash
ddev add-on remove claude-code
ddev restart
```

## License

MIT