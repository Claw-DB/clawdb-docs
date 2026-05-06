# Usage & Limits

Monitor your usage at [clawdb.dev/dashboard/usage](https://clawdb.dev/dashboard/usage).

## Usage alerts

ClawDB sends email notifications when you reach:
- **80%** of any limit — warning notification
- **100%** of any limit — operations above the limit are rate-limited with HTTP 402

Configure notification preferences in **Settings → Notifications**.

## Soft vs hard limits

| Resource | At limit behaviour |
|---|---|
| Memory ops | HTTP 402 returned, ops queued locally |
| Storage | Writes blocked, reads still work |
| Reflect jobs | Job queued, runs when limit resets |
| Workspaces | Creation blocked until upgrade |

Memory op limits reset on the 1st of each month.

## Reading usage via API

```typescript
// Current month usage
const usage = await fetch('https://api.clawdb.dev/v1/usage', {
  headers: { Authorization: `Bearer ${apiKey}` }
})
// { memoryOps: 84219, storageGB: 2.8, reflectJobs: 247, ... }
```
