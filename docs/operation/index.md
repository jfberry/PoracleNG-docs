# Operation

This section covers day-to-day administration and operation of PoracleNG.

## Contents

- **[Admin Commands](admincommands.md)** — commands available to bot administrators
- **[Discord Admin](discord.md)** — Discord-specific admin operations
- **[Telegram Admin](telegram.md)** — Telegram-specific admin operations
- **[Monitoring](monitoring.md)** — health checks, Prometheus metrics, and log files
- **[Rate Limits](ratelimits.md)** — controlling message rates and user limits
- **[FAQ](faq.md)** — frequently asked questions and troubleshooting

## Key Operational Concepts

### Two Components

PoracleNG runs as two processes that must both be healthy:

- **Processor** (port 3030) — receives webhooks, matches users
- **Alerter** (port 3031) — delivers messages, handles commands

Use `./start.sh` to manage both. The script monitors both processes and shuts them down together.

### Logs

Both components write to the `logs/` directory:

| Log File | Source | Content |
|----------|--------|---------|
| `processor.log` | Processor | Webhook processing, matching |
| `general-<date>.log` | Alerter | Main alerter activity |
| `errors-<date>.log` | Alerter | Warnings and errors |
| `discord-<date>.log` | Alerter | Discord message delivery |
| `telegram-<date>.log` | Alerter | Telegram message delivery |
| `commands-<date>.log` | Alerter | User commands |

### Reloading

When tracking data changes (via user commands or API), the alerter signals the processor to reload its in-memory tracking data via `POST /api/reload`. This happens automatically.

The processor also periodically reloads from the database (configurable via `[tuning] reload_interval_secs`).
