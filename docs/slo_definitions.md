# SLO Definitions — trade-signal-api

## Service Level Objectives

### Availability SLO
- Target: 99.9% availability over a 30-day rolling window
- Measurement: percentage of requests returning 2xx responses
- Error budget: 0.1% — approximately 43 minutes of downtime per month
- Alert: SLOBurnRateFast fires when budget burns at 14x normal rate

### Latency SLO
- Target: p99 latency under 200ms for 99% of requests
- Measurement: histogram_quantile(0.99) from Prometheus metrics
- Alert: HighAPILatency fires at 200ms, CriticalAPILatency fires at 500ms

### Cache Hit Rate Target
- Target: above 85% under normal operating conditions
- Measurement: cache_hits / (cache_hits + cache_misses)
- Alert: LowCacheHitRate fires when below 85% for 10 minutes

## Error Budget Policy

When the SLOBurnRateFast alert fires:
1. Freeze all non-critical deployments immediately
2. Page on-call engineer
3. Investigate root cause within 15 minutes
4. Document findings in incident timeline
5. Resume deployments only after error rate returns to normal for 30 minutes
