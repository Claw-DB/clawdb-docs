# Cloud Overview

[clawdb.dev](https://clawdb.dev) is the managed cloud platform for ClawDB. It provides hosted sync, storage, and observability — with zero infrastructure to manage.

## What cloud adds

| Feature | Local only | + Cloud |
|---|---|---|
| Agent memory | ✓ | ✓ |
| Semantic search | ✓ | ✓ |
| Branching | ✓ | ✓ |
| Auto-summarization | ✓ | ✓ |
| Sync across devices | — | ✓ |
| Team workspaces | — | ✓ |
| Dashboard & observability | — | ✓ |
| Managed backups | — | ✓ |
| SLA uptime guarantee | — | ✓ (Pro+) |

## Getting started

1. Go to [clawdb.dev](https://clawdb.dev) and create an account
2. Your workspace and API key are provisioned in < 5 seconds
3. Set `CLAWDB_API_KEY=sk_live_your_key_here` in your environment
4. Run `clawdb()` — auto-connects to the cloud

## Endpoints

| Region | Sync hub URL |
|---|---|
| EU West (Frankfurt) | `https://eu-west-1.sync.clawdb.dev` |
| US East (Virginia) | `https://us-east-1.sync.clawdb.dev` |
| AP Southeast (Singapore) | `https://ap-southeast-1.sync.clawdb.dev` |

The SDK selects the endpoint automatically based on your workspace's region setting.
