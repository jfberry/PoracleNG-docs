# Monitoring

PoracleNG runs as a single processor binary. It exposes a health check, Prometheus metrics, and plain-text logs — all on the processor's configured port (default `3030`).

## Health Check

```sh
curl http://localhost:3030/health
```

Use this for load balancer health checks or external monitoring systems. The Docker image polls it every 30 seconds.

## Prometheus Metrics

```sh
curl http://localhost:3030/metrics
```

### Scrape Configuration

```yaml
scrape_configs:
  - job_name: 'poracle'
    static_configs:
      - targets: ['localhost:3030']
```

The repository ships a starting point at `monitoring/prometheus.yml.example`.

!!! note
    `monitoring/prometheus.yml.example` still contains a second `poracle_alerter` job scraping port 3031. That component no longer exists — delete the job, or Prometheus will report the target as down.

### Key Metrics

All PoracleNG metrics are prefixed `poracle_`. Broadly they cover:

- Webhooks received and processing time, by type
- Matching operations, latency, and matched users
- State reload timing and outcome
- Render queue depth and template render time
- Message delivery (Discord / Telegram / webhooks) — outcomes, latency, queue depths, in-flight sends, Discord rate-limit waits
- Alert rate limiting — `poracle_processor_rate_limit_dropped_total`, `_breaches_total`, `_disabled_total`
- Geocoding, static map tiles, circuit breakers, AccuWeather usage
- Go runtime and process stats (memory, goroutines, CPU)

Run `curl -s http://localhost:3030/metrics | grep '^# HELP poracle_'` to see the full list with descriptions for your build.

### Delivery Lane Metrics

Each destination (user, channel, webhook, Telegram chat) gets its own delivery lane — see [Rate Limits](ratelimits.md#delivery-lanes). These aggregate metrics (no per-target labels) show how the lanes are coping:

| Metric | Type | Meaning |
|--------|------|---------|
| `poracle_delivery_active_lanes` | gauge | Live lanes (spawned minus reaped) |
| `poracle_delivery_lane_queued` | gauge | Total jobs buffered across all lanes |
| `poracle_delivery_lane_max_depth` | gauge | Depth of the deepest single lane — a head-of-line signal |
| `poracle_delivery_lanes_near_capacity` | gauge | Lanes at or above 80% of `delivery_queue_size` |
| `poracle_delivery_lane_backpressure_total` | counter | Enqueues that found a lane full (saturation) |
| `poracle_delivery_lane_shed_total` | counter | Messages discarded to make room on a full lane — **real message loss; alert on this** |
| `poracle_delivery_clean_delete_dropped_total` | counter | Clean-up deletes dropped because a lane was full or shutting down |
| `poracle_delivery_lane_spawned_total` | counter | Lanes created |
| `poracle_delivery_lane_reaped_total` | counter | Lanes removed after 60 s idle |

Shed messages are also counted in `poracle_delivery_total{status="shed_lane_full"}`.

!!! info "PoracleNG 5.3+"
    Shedding (and so `poracle_delivery_lane_shed_total` and `status="shed_lane_full"`) is new in 5.3. In 5.2.x a full lane makes the producer wait instead of dropping anything.

A reasonable alert rule:

```yaml
- alert: PoracleDeliveryShedding
  expr: increase(poracle_delivery_lane_shed_total[10m]) > 0
  labels:
    severity: warning
  annotations:
    summary: "PoracleNG is dropping queued alerts because a destination lane is full"
```

### Grafana Dashboards

Two ready-made dashboards are in the repository under `monitoring/grafana/`:

| File | Contents |
|------|----------|
| `poracle-operations-lite-dashboard.json` | Compact operations view — intake, match rate, delivery outcomes, queues, rate limits, maps/geocoding, runtime |
| `poracle-observability-dashboard.json` | Detailed view — per-type webhook and matching latency, render pipeline, delivery by platform, integrations, circuit breakers, AccuWeather |

Import them in Grafana (**Dashboards → New → Import**) and pick your Prometheus data source when prompted.

!!! note
    The bundled dashboards don't yet chart the per-destination lane metrics above. Add a panel for `poracle_delivery_lane_max_depth`, `poracle_delivery_lanes_near_capacity` and `rate(poracle_delivery_lane_shed_total[5m])` if you want them.

## Log Files

Logs are written to stdout and to the `logs/` directory at the project root.

| File | Content |
|---|---|
| `processor.log` | Main processor log (rotated by size) |
| `webhooks.log` | Raw webhook bodies (only if `[webhookLogging]` is enabled) |

Each line looks like:

```
INFO 2026-10-17 14:02:11 [9876543210123456789] -> Pikachu Hunters 123456789012345678 CHANNEL Sending discord message
```

The level comes first (`DEBG`, `INFO`, `WARN`, `ERRO`). The bracketed reference, when present, is the event's own ID (encounter ID, gym ID, pokestop ID, …), so `grep` for it to follow one webhook through matching, rendering and delivery.

### Log Levels

| Level | Use |
|-------|-----|
| `trace` | Most verbose |
| `debug` | Per-message detail — matching decisions, shed messages, snapshot writes. Large volume on a busy server |
| `info` | **Default.** Startup, deliveries, `[Status]` lines, commands |
| `warn` | Warnings and errors only |

The PoracleJS names are still accepted: `verbose` means `info` and `silly` means `trace`. Before 5.2 neither was recognised, so both quietly gave you `info` — if your config still says `silly`, you now get far more output than before. Set `level = "info"`.

### Log Configuration

See [`[logging]`](../config/logging.md) and [`[webhookLogging]`](../config/webhookLogging.md) for the full field reference.

```toml
[logging]
level = "info"             # trace, debug, info, warn
file_logging_enabled = true
filename = "logs/processor.log"
max_size = 50              # MB
max_age = 7                # days
max_backups = 5
compress = true
```

### Raw Webhook Logging

```toml
[webhookLogging]
enabled = false
filename = "logs/webhooks.log"
max_size = 100             # MB
max_age = 1                # days
max_backups = 12
compress = true
rotate_interval = 60       # minutes (0 = size-based only)
```

## The Status Line

Every `[tuning] reload_interval_secs` (default 60 seconds) the processor reloads state from the database and logs a one-line summary at `info`:

```
INFO 2026-10-17 14:03:00 [Status] Webhooks: 5120/min | Matched: 340/min | Tiles: 41 calls avg:180ms err:0 | Geo: 37 calls avg:95ms hits:210 err:0 | RenderQ: 0/100 | Summary: 12 buffered | Delivery: Discord:3+0 Telegram:0 Tracked:812 RateLimited:0 | Lanes:41 active, 3 queued, deepest=2 (123456789012345678), 0 near-cap
```

| Field | Meaning |
|-------|---------|
| `Webhooks` / `Matched` | Webhooks received and users matched per minute over the interval |
| `Tiles` | Static map tile requests (count, average latency, errors) — only if static maps are configured |
| `Geo` | Geocoder calls, average latency, cache hits, errors |
| `RenderQ` | Render queue depth / capacity (`[tuning] render_queue_size`) |
| `Summary` | Entries buffered for scheduled [alert summaries](../userguide/summaries.md) |
| `Delivery` | Queued Discord sends + Discord webhook sends, Telegram sends, messages tracked for clean/edit, and sends waiting on a platform rate limit |
| `Lanes` | Active destination lanes, total queued, the deepest lane and its target, and lanes at 80%+ capacity |

Two extra lines are logged at `warn` when something is wrong:

- `[Status] No webhooks have been received — check your scanner or networking configuration` — nothing arrived during the interval.
- `[Status] delivery backing up: …` / `[Status] delivery shedding messages: …` — a lane reached 80% of `delivery_queue_size` or filled up since the last sample. "Shedding messages" means alerts have been discarded since startup (5.3+). See [Rate Limits → Delivery Lanes](ratelimits.md#delivery-lanes).

A full lane also logs `delivery: lane full for <target> — shedding oldest queued messages (total shed: N)`, at most once every 5 seconds.

## Troubleshooting

### No alerts being sent

1. Health check: `curl http://localhost:3030/health`
2. Confirm webhooks are arriving — check the `[Status]` line's `Webhooks:` rate in `logs/processor.log`
3. Look for `WARN` / `ERRO` lines
4. Verify the user has areas / location set and tracking configured
5. Check whether the target is rate-limited or disabled: `!poracle-admin ratelimit list`

### High latency

1. Check `RenderQ` and `Lanes` in the `[Status]` line, or the queue-depth metrics
2. Review `[tuning]` settings — see [`[tuning]`](../config/tuning.md)
3. Check database connection performance
4. Check tileserver and geocoder latency (`Tiles` / `Geo` in the status line)

### Memory usage

The processor holds tracking rules in memory. Watch the Go runtime metrics from `/metrics`. If memory is high, look for users with excessive subscriptions. Each active delivery lane can buffer up to `[tuning] delivery_queue_size` alerts, so memory under a backlog scales with the number of destinations.
