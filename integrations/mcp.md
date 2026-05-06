# MCP — Claude Desktop & AI Editors

The `@clawdb/mcp-adapter` exposes ClawDB as an MCP (Model Context Protocol) server, giving any MCP-compatible AI tool persistent memory with zero configuration.

---

## Supported hosts

| Host | Install command |
|---|---|
| Claude Desktop | `npx @clawdb/mcp-adapter --install-claude` |
| Cursor | `npx @clawdb/mcp-adapter --install-cursor` |
| VS Code + Copilot | `npx @clawdb/mcp-adapter --install-vscode` |
| Continue.dev | `npx @clawdb/mcp-adapter --install-continue` |
| Zed | `npx @clawdb/mcp-adapter --install-zed` |

---

## Claude Desktop

### Automatic install

```bash
npx @clawdb/mcp-adapter --install-claude
```

This writes the correct JSON block to `claude_desktop_config.json` automatically. Restart Claude Desktop to activate.

### Manual install

If you prefer to edit the config yourself, run:

```bash
npx @clawdb/mcp-adapter --print-config --host claude
```

Then add the output to your `claude_desktop_config.json`:

**macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`  
**Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "clawdb": {
      "command": "npx",
      "args": ["-y", "@clawdb/mcp-adapter@latest"],
      "env": {
        "CLAWDB_AGENT_ID": "claude-desktop",
        "CLAWDB_API_KEY": "sk_live_your_key_here"
      }
    }
  }
}
```

{% hint style="info" %}
If you have ClawDB running locally (via `clawdb start`), you can omit `CLAWDB_API_KEY` and set `CLAWDB_URL=http://localhost:50050` instead.
{% endhint %}

---

## Available MCP tools

Once connected, Claude can use these tools:

| Tool | Description |
|---|---|
| `clawdb_remember` | Store information that should persist across conversations |
| `clawdb_search` | Search memory by meaning to recall relevant context |
| `clawdb_recall` | Retrieve specific memories by ID |
| `clawdb_branch_fork` | Fork memory state for experimentation |
| `clawdb_branch_merge` | Merge an experiment back into main memory |
| `clawdb_status` | Check ClawDB connection and health |
| `clawdb_remember_bulk` | Store up to 100 memories in one call |

---

## MCP Resources

ClawDB also exposes MCP Resources (readable content):

- `clawdb://memory/{id}` — a specific memory record
- `clawdb://recent` — the 20 most recent memories

---

## MCP Prompts

- `clawdb_load_context` — searches memory for the current conversation topic and returns results formatted as system context

---

## Other editors

### Cursor

```bash
npx @clawdb/mcp-adapter --install-cursor
```

Config written to `~/.cursor/mcp.json`.

### VS Code

```bash
npx @clawdb/mcp-adapter --install-vscode
```

Config written to `.vscode/mcp.json` in the current project.

### Continue.dev

```bash
npx @clawdb/mcp-adapter --install-continue
```

Config written to `~/.continue/config.json`.

### Zed

```bash
npx @clawdb/mcp-adapter --install-zed
```

Config written to `~/.config/zed/settings.json`.

---

## Programmatic usage

```typescript
// The adapter can also be started programmatically
import { createMCPServer } from '@clawdb/mcp-adapter'

const server = await createMCPServer({
  agentId: 'my-agent',
  endpoint: 'http://localhost:50050',
})

server.start()  // starts the stdio MCP server
```
