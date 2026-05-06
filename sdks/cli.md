# CLI Reference

**Package:** `@clawdb/cli`  
**Binary:** `clawdb`

---

## Installation

```bash
# npx (no install needed)
npx clawdb@latest <command>

# Global install
npm install -g @clawdb/cli

# Homebrew
brew install clawdb

# Cargo
cargo install clawdb-cli
```

---

## Global flags

All commands accept these global flags:

| Flag | Description |
|---|---|
| `--base-url <url>` | Server URL. Env: `CLAWDB_BASE_URL` |
| `--token <token>` | Session token. Env: `CLAWDB_SESSION_TOKEN` |
| `--output json\|table\|tsv` | Output format. Defaults to `table`, or `json` when stdout is not a TTY |
| `--quiet` | Suppress all output except errors |

---

## Commands

### `clawdb init`

Bootstrap ClawDB in the current project.

```bash
clawdb init [--cloud] [--data-dir <path>]
```

- Detects project type (Node.js, Python, Rust, Go)
- Downloads and starts the server
- Writes `.clawdb.env`
- Prints the correct SDK snippet for your project
- Optionally installs MCP adapter for Claude Desktop

---

### `clawdb start`

Start the ClawDB server.

```bash
clawdb start [--port 50050] [--detach]
```

`--detach` writes the PID to `~/.clawdb/server.pid` and exits.

---

### `clawdb stop`

```bash
clawdb stop
```

Sends SIGTERM to the server process, waits for graceful shutdown.

---

### `clawdb status`

Check health of all components.

```bash
clawdb status [--json]
```

```
✓ clawdb-server    localhost:50050   healthy  (4ms)
✓ claw-vector-svc  localhost:8081    ready    (12ms)
✓ sync             cloud.clawdb.dev  online
⚠ reflect          not configured
```

---

### `clawdb memory`

```bash
# Search
clawdb memory search "deployment policies" [--top-k 10] [--json]

# Store
clawdb memory remember "content" [--type fact] [--tags ops,deploy]

# From file
clawdb memory remember --file ./notes.txt

# List
clawdb memory list [--type fact] [--limit 50]

# Delete
clawdb memory delete <id>
```

---

### `clawdb session`

```bash
# Create
clawdb session create --agent-id my-agent --role assistant --scopes memory:write,memory:read

# Validate current token
clawdb session whoami

# Revoke
clawdb session revoke <session-id>
```

---

### `clawdb branch`

```bash
clawdb branch fork <name> [--from <branch-id>]
clawdb branch list [--status active|merged|discarded]
clawdb branch merge <source-id> <target-id> [--strategy last-write|source-wins]
clawdb branch diff <source-id> <target-id>
clawdb branch discard <branch-id>
```

---

### `clawdb sync`

```bash
clawdb sync [--dry-run]
```

```
↑ 42 pushed   ↓ 7 pulled   △ 0 conflicts   (312ms)
```

---

### `clawdb reflect`

```bash
clawdb reflect [--job summarise|extract|decay|all] [--dry-run]
```

Shows a progress spinner. Prints stats on completion.

---

### `clawdb mcp`

Install ClawDB as an MCP server for AI editors.

```bash
# Install for specific editors
clawdb mcp install-claude
clawdb mcp install-cursor
clawdb mcp install-vscode
clawdb mcp install-continue
clawdb mcp install-zed

# Print config JSON without installing
clawdb mcp print-config --host claude
```

---

### `clawdb config`

```bash
clawdb config show              # show all config (redacts secrets)
clawdb config get base_url      # get a single value
clawdb config set base_url http://localhost:50050
```

---

### `clawdb completion`

Generate shell completion scripts.

```bash
clawdb completion bash   >> ~/.bashrc
clawdb completion zsh    >> ~/.zshrc
clawdb completion fish   > ~/.config/fish/completions/clawdb.fish
clawdb completion pwsh   >> $PROFILE
```
