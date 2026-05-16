# MCP — Claude Desktop & AI Editors

ClawDB ships an MCP server via `@clawdb/mcp-adapter` so MCP-compatible hosts can access persistent memory, branches, sync, and reflection tools.

---

## Recommended install path

Use the ClawDB CLI to install host-specific MCP config:

```bash
clawdb mcp install-claude
clawdb mcp install-cursor
clawdb mcp install-vscode
clawdb mcp install-zed
clawdb mcp install-openclaw
clawdb mcp install-google-antigravity
```

The CLI writes host-correct config formats and paths automatically.

---

## Supported hosts

| Host | Install command |
|---|---|
| Claude Desktop | `clawdb mcp install-claude` |
| Cursor | `clawdb mcp install-cursor` |
| VS Code + Copilot | `clawdb mcp install-vscode` |
| Zed | `clawdb mcp install-zed` |
| OpenClaw | `clawdb mcp install-openclaw` |
| Google Antigravity | `clawdb mcp install-google-antigravity` |

---

## Manual config block

If you need to inspect the base config block:

```bash
clawdb mcp print-config --host claude
```

Example output:

```json
{
  "mcpServers": {
    "clawdb": {
      "command": "npx",
      "args": ["-y", "@clawdb/mcp-adapter@latest"],
      "env": {
        "CLAWDB_ENDPOINT": "http://localhost:50050",
        "CLAWDB_AGENT_ID": "claude"
      }
    }
  }
}
```

Note: some hosts use different top-level keys (`servers` in VS Code, `context_servers` in Zed). Prefer `clawdb mcp install-*` unless you are customizing manually.

---

## Host restart requirement

After install, fully restart your host/editor so the new MCP server config is loaded.

---

## Available MCP tools

Once connected, your host can call tools such as:

- `clawdb_remember`
- `clawdb_remember_bulk`
- `clawdb_search`
- `clawdb_recall`
- `clawdb_branch_fork`
- `clawdb_branch_merge`
- `clawdb_sync`
- `clawdb_reflect`

The exact tool list is exposed by the MCP server at runtime.

---

## Running the adapter directly

You can launch the stdio MCP server directly:

```bash
npx -y @clawdb/mcp-adapter@latest
```

For local mode, ensure `CLAWDB_ENDPOINT=http://localhost:50050` is set (or use `.clawdb.env` from `clawdb init`).
