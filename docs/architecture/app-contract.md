# App Contract — the platform-agnostic rules

The application is scenery; **this contract is the curriculum.** Any deployment target (App Service, Container Apps, VMSS) must be able to run the same image satisfying everything below. A target that can't satisfy the contract is a finding, not an app change.

## Shape
- Single container image (linux/amd64) published to **ghcr.io** (free, $0 standing).
- **Stateless.** All persistent state in Azure SQL. No local disk, no session affinity, no side-effects outside the request.
- Config via environment variables only (App Settings / CA env / VMSS cloud-init env file). Secrets referenced via **user-assigned managed identity → Key Vault** (auth model uniform on all three targets).

## Endpoints (contract)
| Route | Purpose |
|---|---|
| `GET /` | tiny UI: form to shorten |
| `POST /api/shorten` | `{url}` → `{code}`; validates URL, 4xx on junk |
| `GET /{code}` | 302 redirect; 404 unknown; **counts the hit in SQL** |
| `GET /healthz` | liveness — process up, returns 200, no dependencies |
| `GET /readyz` | readiness — SQL reachable (timeout ≤ 2 s, fail → 503) |
| `GET /metrics` | counters: requests, shorten count, redirect count, db latency histogram (Prometheus format) |
| `POST /admin/fault?mode=crash\|hang\|500\|slow` | **fault injection for drills** — only enabled when env `FAULT_INJECTION=1`; never set in "prod" tags |

## Behavioral requirements
- Structured JSON logs to stdout (request id, latency, result) — collected identically by all platforms' log pipelines.
- Graceful shutdown: honor SIGTERM, drain ≤ 10 s (drill-relevant on VMSS/CA scale-in).
- Data-plane auth to SQL via Entra token from the platform's MI endpoint (uniform across targets).
- P95 redirect latency target < 150 ms in-region (baseline for chaos/cost reports).

## Acceptance gate
`scripts/smoke.sh` runs against any deployment: healthz/readyz green, shorten→redirect round-trip works, metrics increment, readyz fails within 2 s when SQL is blocked, graceful drain observed. **The script is the same on all three platforms — the differences you see in its output are the findings.**

## Deliberately excluded (closed — do not grow)
Authn/authz for users, HTTPS hardening (platform's job), queues, caches, UI polish, the short-code alphabet bikeshed. Build in Lab 3 with AI assistance; cap: one session.
