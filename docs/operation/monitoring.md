# Monitoring

PoracleNG provides health checks, Prometheus metrics, and structured logging for monitoring.

## Health Checks

Both components expose a `/health` endpoint:

```sh
curl http://localhost:3030/health   # Processor
curl http://localhost:3031/health   # Alerter
```

Use these for load balancer health checks or monitoring systems.

## Prometheus Metrics

Both components expose Prometheus-compatible metrics at `/metrics`:

```sh
curl http://localhost:3030/metrics  # Processor metrics
curl http://localhost:3031/metrics  # Alerter metrics
```

### Scrape Configuration

Add both endpoints to your Prometheus config:

```yaml
scrape_configs:
  - job_name: 'poracle-processor'
    static_configs:
      - targets: ['localhost:3030']

  - job_name: 'poracle-alerter'
    static_configs:
      - targets: ['localhost:3031']
```

### Key Metrics

**Processor metrics** include:

- Webhooks received (by type)
- Matching operations and latency
- User match counts
- Reload timing
- Memory usage

**Alerter metrics** include:

- Messages sent (by platform, type)
- Message delivery latency
- Rate limit hits
- Queue depth
- API request counts

## Log Files

Both components write to the `logs/` directory at the project root.

### Processor Logs

| File | Content |
|------|---------|
| `processor.log` | Main processor log (rotated by size) |
| `webhooks.log` | Raw webhook bodies (if enabled) |

### Alerter Logs

| File | Content |
|------|---------|
| `general-<date>.log` | Main alerter log (rotated daily) |
| `errors-<date>.log` | Warnings and errors only |
| `discord-<date>.log` | Discord message delivery |
| `telegram-<date>.log` | Telegram message delivery |
| `commands-<date>.log` | User commands |
| `controller-<date>.log` | Controller activity |
| `matched_webhooks-<date>-<hour>.log` | Matched results (if enabled) |

### Log Configuration

```toml
[logging]
level = "verbose"                    # silly, debug, verbose, info, warn
max_age = 7                          # Days to keep log files
max_size = 50                        # Max file size in MB
max_backups = 5                      # Old files to keep
compress = false                     # Gzip rotated logs
webhook_log_limit = 12               # Hours to keep hourly webhook logs

[logging.enable_logs]
webhooks = false                     # Matched webhook log (can be large)
discord = true                       # Discord delivery log
telegram = true                      # Telegram delivery log
pvp = false                          # PVP calculations (verbose)
```

### Raw Webhook Logging

Enable logging of raw webhook bodies received from Golbat (useful for debugging):

```toml
[webhookLogging]
enabled = false
filename = "logs/webhooks.log"
max_size = 100                       # MB
max_age = 1                          # Days
max_backups = 3
```

## Troubleshooting

### No Alerts Being Sent

1. Check processor health: `curl http://localhost:3030/health`
2. Check alerter health: `curl http://localhost:3031/health`
3. Verify webhooks are arriving: check `logs/processor.log`
4. Check for errors: `logs/errors-*.log`
5. Verify user has areas/location set and tracking configured

### High Latency

1. Check queue depth in Prometheus metrics
2. Review `[tuning]` settings
3. Check database connection performance
4. Consider increasing worker counts

### Memory Usage

The processor holds all tracking rules in memory. Monitor via Prometheus metrics. If memory is high, review tracking data for users with excessive subscriptions.
