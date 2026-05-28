# On-Call Runbook — trade-signal-api

## Alert Response Procedures

### HighAPILatency / CriticalAPILatency

Step 1 — Check Redis connectivity:
Open Grafana dashboard. Check Cache Hit Rate panel.
If cache hit rate is dropping, Redis may be degraded.

Step 2 — Check Redis slow log:
    redis-cli -h localhost -p 6379 SLOWLOG GET 10

Step 3 — Check connection count:
    redis-cli -h localhost -p 6379 INFO clients

Step 4 — Check API error logs:
    docker logs trade-signal-api --tail 100

Step 5 — If Redis is the cause, restart Redis and monitor recovery.
Step 6 — If API is the cause, check for recent deployments and rollback if needed.

---

### HighErrorRate / CriticalErrorRate

Step 1 — Identify which endpoint is failing:
Check Grafana — Request Rate panel broken down by endpoint and status.

Step 2 — Check application logs:
    docker logs trade-signal-api --tail 200

Step 3 — Check Redis connectivity:
    redis-cli -h localhost -p 6379 PING

Step 4 — If Redis is down, restart it:
    docker compose restart redis

Step 5 — If API is throwing 500s, check for recent code deployments.

---

### SLOBurnRateFast

Step 1 — Check current error rate in Grafana.
Step 2 — Freeze all non-critical deployments.
Step 3 — Follow HighErrorRate procedure above.
Step 4 — Document incident timeline with timestamps.

---

## Incident Timeline Template

    [HH:MM] Alert fired — <alert name>
    [HH:MM] On-call engineer paged
    [HH:MM] Investigation started
    [HH:MM] Root cause identified — <description>
    [HH:MM] Fix applied — <description>
    [HH:MM] Service recovered — error rate returned to normal
    [HH:MM] Incident closed
