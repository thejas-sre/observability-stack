# observability-stack

Full observability stack for monitoring trade-signal-api in production.
Reduced incident post-mortem reconstruction time by 75% (4-6 hours to approximately
90 minutes) through real-time API latency and error rate visibility with SLO-based alerting.

---

## Problem Statement

Without centralised observability, incident reconstruction required manual log
grep across multiple hosts with no latency or error rate visibility. Post-mortem
reconstruction took 4 to 6 hours. This stack provides real-time visibility into
trade-signal-api performance, fires alerts when SLO thresholds are breached, and
enables incident timeline reconstruction in under 90 minutes.

---

## Architecture

    trade-signal-api (/metrics endpoint)
            |
            v (scrape every 10s)
        Prometheus
            |
            +---> Grafana (dashboards + visualisation)
            |
            +---> AlertManager (routing + notifications)
                        |
                        +---> WARNING channel
                        +---> CRITICAL channel (page on-call)

---

## How To Run

Prerequisites: Docker, Docker Compose

    git clone https://github.com/thejas-sre/observability-stack.git
    cd observability-stack
    docker compose up -d

Access the stack:

    Grafana:      http://localhost:3000  (admin / admin)
    Prometheus:   http://localhost:9090
    AlertManager: http://localhost:9093

View the trade-signal-api dashboard in Grafana:
Dashboards -> Trade Signal API

---

## Alert Rules

| Alert | Condition | Severity |
|---|---|---|
| HighAPILatency | p99 latency above 200ms for 5 min | WARNING |
| CriticalAPILatency | p99 latency above 500ms for 2 min | CRITICAL |
| HighErrorRate | Error rate above 1% for 5 min | WARNING |
| CriticalErrorRate | Error rate above 5% for 2 min | CRITICAL |
| LowCacheHitRate | Cache hit rate below 85% for 10 min | WARNING |
| SLOBurnRateFast | Error budget burning at 14x normal rate | CRITICAL |

---

## SLO Definitions

- Availability SLO: 99.9% — service returns 2xx to 99.9% of requests over 30 days
- Latency SLO: p99 under 200ms for 99% of requests
- Cache hit rate target: above 85% under normal load

Full SLO definitions and error budget policy in docs/slo_definitions.md

---

## What This Project Demonstrates

- Prometheus scraping real application metrics from trade-signal-api
- Grafana dashboards for API latency, error rate, cache hit rate, and throughput
- AlertManager with severity-based routing and inhibition rules
- SLO-based alerting including error budget burn rate detection
- On-call runbook documenting response procedures for each alert type
- Incident timeline reconstruction guide explaining 75% MTTR reduction

---

## Compliance Considerations

- docs/slo_definitions.md documents SLO targets and error budget policy
- docs/on_call_runbook.md provides structured incident response procedures
- docs/incident_timeline_guide.md documents how post-incident reviews are conducted
- All alert thresholds are explicitly documented with rationale

---

## Related Projects

| Project | Connection |
|---|---|
| trade-signal-api | The service this stack monitors |
| redis-performance-toolkit | Validates Redis before production — prevents latency alerts |
| release-validation-pipeline | Gates deployments that could trigger error rate alerts |
