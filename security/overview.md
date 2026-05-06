# Security Overview

ClawDB is designed with security at every layer.

## Threat model

ClawDB assumes:
- The local filesystem is trusted (the agent controls it)
- The network between agent and hub is untrusted (all sync is encrypted)
- API keys may be compromised (key rotation is zero-downtime)
- Agents may attempt to access data beyond their scope (Guard enforces scopes)

## Security layers

| Layer | Mechanism |
|---|---|
| Authentication | API keys (BLAKE3-hashed), JWT sessions |
| Authorization | Intent-aware policy engine (claw-guard) |
| Transport | TLS + XSalsa20-Poly1305 + Ed25519 |
| Audit | BLAKE3-chained append-only audit log |
| At-rest | Optional SQLCipher encryption (`encryption` feature) |
| Secrets | JWT secrets zeroized on drop, never logged |

## Responsible disclosure

If you discover a security vulnerability, do **not** file a public GitHub issue. Email security@clawdb.dev with:
- A description of the vulnerability
- Steps to reproduce
- Your assessment of the impact

We respond within 48 hours and coordinate disclosure.
