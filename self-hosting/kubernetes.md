# Kubernetes

For production-scale deployments, ClawDB includes Kubernetes manifests in `packages/infra/k8s/`.

## Quick deploy

```bash
# Create namespace
kubectl apply -f k8s/namespace.yaml

# Create secrets (edit first!)
kubectl apply -f k8s/secrets/example.yaml  # NEVER use example values in production

# Deploy
kubectl apply -f k8s/
```

## Manifests included

| File | Description |
|---|---|
| `namespace.yaml` | Creates `clawdb` namespace |
| `api/deployment.yaml` | clawdb-server, 2 replicas, HPA 2–10 |
| `worker/deployment.yaml` | BullMQ workers, 1–4 replicas |
| `redis/deployment.yaml` | Redis 7 with persistence |
| `ingress.yaml` | TLS ingress via cert-manager |
| `secrets/example.yaml` | Example secrets (placeholder values only) |

## Resource requests

| Component | CPU request | Memory request | CPU limit | Memory limit |
|---|---|---|---|---|
| API server | 250m | 256 Mi | 1000m | 512 Mi |
| Worker | 250m | 256 Mi | 1000m | 1 Gi |

## Health probes

```yaml
livenessProbe:
  httpGet:
    path: /v1/health
    port: 8080
  initialDelaySeconds: 15

readinessProbe:
  httpGet:
    path: /v1/health
    port: 8080
  initialDelaySeconds: 5
```

## TLS

Configure cert-manager with Let's Encrypt. The ingress manifest includes the required annotations.
