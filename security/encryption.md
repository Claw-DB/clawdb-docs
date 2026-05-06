# Encryption at Rest

By default, ClawDB's local SQLite database is **not** encrypted at rest. The file is protected only by filesystem permissions.

## Enabling encryption

Build `claw-core` with the `encryption` feature:

```toml
[dependencies]
claw-core = { version = "0.1", features = ["encryption"] }
```

{% hint style="warning" %}
The `encryption` feature requires the **SQLCipher** build of SQLite, not the standard SQLite. You must link against SQLCipher when building.
{% endhint %}

Then provide the encryption key:

```rust
let engine = ClawEngine::open(ClawConfig {
    db_path:        "~/.clawdb/main.db".into(),
    encryption_key: Some([0u8; 32]),  // your 32-byte key
    ..Default::default()
}).await?;
```

The encryption key is stored as `[u8; 32]` and implements `ZeroizeOnDrop` — it is securely erased from memory when the config is dropped.

## Key rotation

```rust
engine.rotate_key(old_key, new_key).await?;
// Executes PRAGMA rekey on the existing connection
```

## Network encryption

All sync communication between agents and the cloud hub is encrypted with XSalsa20-Poly1305 + Ed25519. This is always on and requires no configuration.
