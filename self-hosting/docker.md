# Docker Compose

The recommended way to self-host ClawDB.

## Prerequisites

- Docker Engine 24+
- Docker Compose v2
- 2 GB RAM minimum
- Ports 50050, 8080, 9090 available

## Setup

```bash
# 1. Download the compose file
curl -O https://raw.githubusercontent.com/Claw-DB/ClawDB/main/docker-compose.yml
curl -O https://raw.githubusercontent.com/Claw-DB/ClawDB/main/.env.example

# 2. Configure secrets
cp .env.example .env
```

Edit `.env` — required fields:

```bash
POSTGRES_PASSWORD=change_me_in_production
CLAW_GUARD_JWT_SECRET=at_least_32_random_characters_here
REFLECT_LLM_API_KEY=sk-ant-...   # Anthropic API key for reflect jobs
```

```bash
# 3. Start all services
docker compose up -d

# 4. Check status
docker compose ps
curl http://localhost:8080/v1/health
```

## Services

| Service | Port | Description |
|---|---|---|
| `clawdb-server` | 50050 (gRPC), 8080 (HTTP) | Main API server |
| `claw-vector-svc` | 8081 | Embedding service |
| `claw-reflect` | 8090 | Summarization workers |
| `postgres` | 5432 (internal) | Durable storage |
| `redis` | 6379 (internal) | Cache + queue |

## Production overlay

For production, use the prod overlay which removes build directives and pins image versions:

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

## Resource limits

The default `docker-compose.yml` includes resource limits:

| Service | Memory | CPU |
|---|---|---|
| clawdb-server | 512 MB | 1.0 |
| claw-vector-svc | 2 GB | 2.0 |
| claw-reflect | 1 GB | 2.0 |
| postgres | 1 GB | — |
| redis | 256 MB | — |
