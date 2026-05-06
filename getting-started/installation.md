# Installation

ClawDB has three installation paths depending on your setup.

---

## Path A — `npx clawdb init` (recommended for new projects)

No pre-installation needed. Run this once in your project:

```bash
npx clawdb@latest init
```

This installs the ClawDB CLI globally via npx, downloads the correct server binary for your platform, and writes connection config to `.clawdb.env`. See [Quick Start](quickstart.md) for the full flow.

---

## Path B — Global CLI install

Install the CLI globally for repeated use:

```bash
# npm
npm install -g @clawdb/cli

# Homebrew (macOS / Linux)
brew install clawdb

# Cargo (if you have Rust)
cargo install clawdb-cli
```

Then run `clawdb start` in any project.

---

## Path C — Docker (production / self-hosted)

The fastest way to run the full stack locally or on a server:

```bash
# Download the compose file
curl -O https://raw.githubusercontent.com/Claw-DB/ClawDB/main/docker-compose.yml

# Copy and fill in secrets
cp .env.example .env
# Edit .env: set POSTGRES_PASSWORD, JWT_SECRET, etc.

# Start everything
docker compose up -d
```

Services started:
- `clawdb-server` — gRPC on `:50050`, HTTP on `:8080`, metrics on `:9090`
- `claw-vector-svc` — embedding service
- `claw-reflect` — summarization workers
- `postgres` — durable storage
- `redis` — cache + queue

---

## Platform support

| Platform | Binary | Docker |
|---|---|---|
| Linux x86\_64 | ✓ | ✓ |
| Linux aarch64 | ✓ | ✓ |
| macOS x86\_64 | ✓ | ✓ |
| macOS arm64 (Apple Silicon) | ✓ | ✓ |
| Windows x86\_64 | ✓ | ✓ |

---

## SDK installation

Each SDK is published independently. Install only what you need:

| Language | Install |
|---|---|
| TypeScript / Node.js | `npm install @clawdb/sdk` |
| Python | `pip install clawdb` |
| Rust | `cargo add clawdb` (embedded) or `cargo add clawdb-client` (HTTP/gRPC client) |
| Go | `go get github.com/Claw-DB/claw-sdk/sdks/go` |

---

## Verifying the installation

```bash
clawdb status
```

Expected output:

```
✓ clawdb-server    localhost:50050   healthy
✓ claw-vector-svc  localhost:8081    embeddings ready
✓ sync             localhost         local-only mode
```

{% hint style="warning" %}
If you see `connection refused`, run `clawdb start` first, or check that the server is running with `clawdb status --json`.
{% endhint %}
