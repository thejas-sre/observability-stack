# observability-stack

Full observability stack for monitoring trade-signal-api in production.
Reduced incident post-mortem reconstruction time by 75% (4–6 hours → ~90 minutes)
through real-time API latency and error rate visibility with SLO-based alerting.

## Status

> Work in progress — full implementation coming shortly.

## What This Project Demonstrates

- Prometheus scraping trade-signal-api metrics endpoint
- Grafana dashboards — API latency, cache hit rate, error rate, SLO burn rate
- AlertManager rules — WARNING and CRITICAL thresholds for latency and error rate
- On-call runbook — response procedures for each alert type
- SLO definitions — 99.9% availability, p99 latency under 200ms

## Alert Rules

| Alert | Condition | Severity |
|---|---|---|
| HighAPILatency | p99 > 200ms for 5 min | WARNING |
| CriticalAPILatency | p99 > 500ms for 2 min | CRITICAL |
| RedisCacheDown | Health check failing | CRITICAL |
| SLOBurnRateFast | Budget burning 14x normal | CRITICAL |

## Stack

Prometheus · Grafana · AlertManager · Docker Compose · Python
