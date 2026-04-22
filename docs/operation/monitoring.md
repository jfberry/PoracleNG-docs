# Monitoring

PoracleNG runs as a single processor binary. It exposes a health check, Prometheus metrics, and structured logs — all on the processor's configured port (default `3030`).

## Health Check

```sh
curl http://localhost:3030/health
```

Use this for load balancer health checks or external monitoring systems.

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

### Key Metrics

- Webhooks received (by type)
- Matching operations and latency
- User match counts
- Reload timing
- Message delivery (Discord / Telegram, by type)
- Rate-limit hits and queue depth
- Memory and goroutine counts

Run `curl http://localhost:3030/metrics` and grep for `poracle_` to see the full list available on your build.

## Log Files

Logs are written to the `logs/` directory at the project root.

| File | Content |
|---|---|
| `processor.log` | Main processor log (rotated by size) |
| `webhooks.log` | Raw webhook bodies (only if `[webhookLogging]` is enabled) |

### Log Configuration

See [`[logging]`](../config/logging.md) and [`[webhookLogging]`](../config/webhookLogging.md) for the full field reference.

```toml
[logging]
level = "verbose"          # silly, debug, verbose, info, warn
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

## Troubleshooting

### No alerts being sent

1. Health check: `curl http://localhost:3030/health`
2. Confirm webhooks are arriving — check `logs/processor.log`
3. Look for errors at log level `warn` or above
4. Verify the user has areas / location set and tracking configured

### High latency

1. Check queue depth in Prometheus metrics
2. Review `[tuning]` settings — see [`[tuning]`](../config/tuning.md)
3. Check database connection performance
4. Consider increasing worker counts

### Memory usage

The processor holds tracking rules in memory. Watch the Go runtime metrics from `/metrics`. If memory is high, look for users with excessive subscriptions.
