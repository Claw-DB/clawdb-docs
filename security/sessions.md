# Sessions & TOTP

## Agent sessions

Sessions are the unit of authorization in ClawDB. Every operation is associated with a session.

```typescript
const session = await db.session.create({
  agentId: 'my-agent',
  role:    'assistant',
  scopes:  ['memory:write', 'memory:read'],
  ttlSecs: 3600,  // default: 1 hour
})
```

Sessions are signed HS256 JWTs. The raw token is passed by the agent on every gRPC or HTTP call via the `x-claw-session` metadata header.

## Session security

- JWT secret is stored as a `SecretString` (zeroized on drop)
- JWT secret never appears in logs or tracing spans
- Revoked sessions are checked against the database on every request
- Refresh tokens are stored as BLAKE3 hashes, rotated on use

## TOTP for cloud accounts

For clawdb.dev accounts, enable TOTP 2FA:

1. Go to **Settings → Security → Two-factor authentication**
2. Scan the QR code with your authenticator app (Google Authenticator, Authy, 1Password)
3. Enter the 6-digit code to confirm
4. Save the backup codes — they are shown once only

### Backup codes

8 single-use backup codes are generated when TOTP is enabled. If you lose access to your authenticator, use a backup code to log in, then re-enroll.

## Active sessions

View and revoke active browser sessions at **Settings → Security → Active sessions**. "Revoke all other sessions" immediately invalidates all sessions except the current one.
