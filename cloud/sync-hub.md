# Sync Hub

The sync hub is the cloud component that receives and distributes encrypted sync deltas from your agents.

## How it works

```
Device A  →  encrypt chunks  →  push to hub  →  hub stores in hub_memory_log
                                                          ↓
Device B  ←  decrypt chunks  ←  pull from hub (since last cursor)
```

The hub never sees the decrypted content of your memories. It stores encrypted `SyncChunk` blobs and routes them by `workspace_id`.

## Registered devices

Each device that syncs registers its Ed25519 public key with the hub. You can see all registered devices at [clawdb.dev/dashboard/sync](https://clawdb.dev/dashboard/sync).

## Audit chain verification

The hub maintains a BLAKE3-chained audit log of all push and pull operations. Verify integrity at any time:

```bash
clawdb sync verify-audit
```

## Conflict handling

When two devices write to the same memory field concurrently, a conflict is detected via HLC comparison and stored in `sync_conflicts`. Conflicts are visible in the Sync Hub dashboard and resolvable via the CLI or API.
