# Incident Timeline Reconstruction Guide

## How To Reconstruct An Incident Timeline

When a post-incident review is required, use this procedure to
reconstruct exactly what happened and when.

### Step 1 — Identify the time window
Check Grafana for when the alert fired.
Note the start time and end time of the incident.

### Step 2 — Pull Prometheus data for the window
In Grafana, set the time range to cover 30 minutes before the alert
through 30 minutes after resolution.

Key metrics to check:
- trade_signal_request_duration_seconds (latency)
- trade_signal_requests_total by status (error rate)
- trade_signal_cache_hits_total vs cache_misses_total

### Step 3 — Pull application logs
    docker logs trade-signal-api --since <start_time> --until <end_time>

### Step 4 — Check Redis state at time of incident
    redis-cli INFO all > redis_state_at_incident.txt

### Step 5 — Assemble the timeline
Format: [timestamp] [component] [observation]

Example:
    [10:32:01] Prometheus — p99 latency exceeded 200ms threshold
    [10:32:31] AlertManager — HighAPILatency alert fired
    [10:33:00] On-call — investigation started
    [10:35:12] Redis — SLOWLOG showed 47 slow queries
    [10:36:00] Redis — restarted, slow queries cleared
    [10:36:45] API — p99 latency returned below 100ms
    [10:37:00] AlertManager — alert resolved

### Why This Reduced Reconstruction Time From 4-6 Hours to 90 Minutes

Before this observability stack, incident reconstruction required:
- Manual log grep across multiple hosts
- No centralised latency or error rate visibility
- No alert history or timeline

With Prometheus and Grafana in place, the full metric history is
available in one dashboard. Reconstruction time dropped from
4-6 hours to approximately 90 minutes.
