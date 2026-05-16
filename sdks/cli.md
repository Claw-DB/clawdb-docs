# CLI Reference

**Package:** `@clawdb/cli`  
**Binary:** `clawdb`

---

## Installation

```bash
# Run directly without installing
npx @clawdb/cli <command>

# Or install globally
npm install -g @clawdb/cli
```

---

## Top-level commands

```bash
clawdb init
clawdb start
clawdb stop
clawdb status
clawdb ready
clawdb session ...
clawdb memory ...
clawdb branch ...
clawdb sync ...
clawdb reflect ...
clawdb tx ...
clawdb cloud ...
clawdb mcp ...
clawdb version
```

Use `--help` on any command to inspect its current options.

---

## `clawdb init`

Bootstraps ClawDB for the current project and runs a smoke test.

```bash
clawdb init [--cloud] [--data-dir <path>] [--json]
```

What it does:

- Detects project type (Node, Python, Go, Rust)
- Auto-provisions a local server (or cloud endpoint with `--cloud`)
- Writes `.clawdb.env`
- Prints SDK usage snippet
- Offers optional MCP host install (Claude, Cursor, VS Code, Zed, OpenClaw, Google Antigravity)

---

## Local server commands

```bash
clawdb start [--port <n>] [--detach] [--json]
clawdb stop [--json]
clawdb status [--json]
clawdb ready [--json]
```

---

## `memory` commands

```bash
clawdb memory remember <content> [--json]
clawdb memory remember-typed <content> [--type <t>] [--tags a,b] [--json]
clawdb memory update <id> <content> [--json]
clawdb memory search <query> [--top-k <n>] [--json]
clawdb memory recall <ids...> [--json]
clawdb memory list [--type <t>] [--limit <n>] [--json]
clawdb memory delete <id> [--json]
```

---

## `session` commands

```bash
clawdb session create [--role <role>] [--scopes s1,s2] [--ttl <secs>] [--json]
clawdb session validate [--json]
clawdb session revoke <sessionId> [--json]
clawdb session count [--json]
```

---

## `branch` commands

```bash
clawdb branch fork <name> [--from <branchId>] [--json]
clawdb branch list [--json]
clawdb branch get <id> [--json]
clawdb branch get-by-name <name> [--json]
clawdb branch trunk [--json]
clawdb branch diff <id> [--target <branchId>] [--json]
clawdb branch merge <source> [--target <branchId>] [--strategy <s>] [--json]
clawdb branch discard <id> [--json]
clawdb branch archive <id> [--json]
```

---

## `sync` commands

```bash
clawdb sync run [--json]
clawdb sync push [--json]
clawdb sync pull [--json]
clawdb sync reconcile [--json]
clawdb sync status [--json]
```

---

## `reflect` commands

```bash
clawdb reflect run [--watch] [--json]
clawdb reflect jobs [--agent-id <id>] [--status <s>] [--limit <n>] [--offset <n>] [--json]
clawdb reflect job <jobId> [--json]
clawdb reflect facts <agentId> [--json]
clawdb reflect preferences <agentId> [--json]
clawdb reflect contradictions <agentId> [--json]
clawdb reflect resolve <agentId> <contradictionId> [--strategy <s>] [--merged-value <json>] [--json]
```

---

## `tx` commands

```bash
clawdb tx begin [--json]
clawdb tx remember <txId> <content> [--json]
clawdb tx remember-typed <txId> <content> [--type <t>] [--tags a,b] [--json]
clawdb tx commit <txId> [--json]
clawdb tx rollback <txId> [--json]
```

---

## `cloud` commands

```bash
clawdb cloud login [--json]
clawdb cloud logout [--json]
clawdb cloud status [--json]
```

---

## `mcp` commands

Install ClawDB MCP config into host/editor settings:

```bash
clawdb mcp install-claude
clawdb mcp install-cursor
clawdb mcp install-vscode
clawdb mcp install-zed
clawdb mcp install-openclaw
clawdb mcp install-google-antigravity
```

Print the generic config block:

```bash
clawdb mcp print-config --host claude
```
