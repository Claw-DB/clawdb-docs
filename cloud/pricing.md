# Pricing & Plans

| | Free | Starter | Pro | Enterprise |
|---|---|---|---|---|
| Price | $0/mo | $9/mo | $29/mo | Custom |
| Memory ops | 10k/mo | 500k/mo | Unlimited | Unlimited |
| Storage | 500 MB | 5 GB | 50 GB | Unlimited |
| Workspaces | 1 | 1 | 10 | Unlimited |
| Cloud sync | — | ✓ | ✓ | ✓ |
| Team members | 1 | 1 | Unlimited | Unlimited |
| Reflect jobs | 10/mo | 100/mo | Unlimited | Unlimited |
| Audit log | — | 7 days | 90 days | 1 year |
| Support | Community | Email | Priority | Dedicated |
| SLA | — | — | 99.9% | Custom |
| SSO / SCIM | — | — | — | ✓ |
| Custom data residency | — | — | — | ✓ |

All plans include the full open-source engine (Apache-2.0). Cloud plans add managed hosting, sync, and support on top.

## Upgrading

Go to [clawdb.dev/dashboard/billing](https://clawdb.dev/dashboard/billing) to upgrade. Changes take effect immediately and are prorated.

## What counts as a memory op?

Each of the following counts as one operation against your monthly limit:
- `db.memory.remember()` — 1 op
- `db.memory.search()` — 1 op per query
- `db.memory.recall()` — 1 op per ID retrieved
- `db.memory.delete()` — 1 op
- `db.reflect()` — counted separately as a reflect job
