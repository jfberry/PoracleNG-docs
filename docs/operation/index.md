# Operation

This section covers day-to-day administration and operation of PoracleNG.

## Contents

- **[Admin Commands](admincommands.md)** — commands available to bot administrators, including `!poracle-admin` (`!pa`)
- **[Discord Admin](discord.md)** — Discord-specific admin operations
- **[Telegram Admin](telegram.md)** — Telegram-specific admin operations
- **[Monitoring](monitoring.md)** — health checks, Prometheus metrics, Grafana dashboards, and log files
- **[Rate Limits](ratelimits.md)** — per-destination message limits, delivery lanes, and auto-disable
- **[Upgrading & Release Notes](upgrading.md)** — what changes between versions and what to check before upgrading
- **[FAQ](faq.md)** — frequently asked questions and troubleshooting

## Key Operational Concepts

### One Process

PoracleNG is a single Go binary (`processor/poracle-processor`) listening on one port (default `3030`). It receives scanner webhooks, matches them against tracking rules held in memory, renders and delivers alerts, runs the Discord and Telegram bots, and serves the REST API, `/health` and `/metrics`.

Run it with `./start.sh` (which builds the binary if it is missing, then `exec`s it), with PM2 via the bundled `ecosystem.config.js`, or in [Docker](../installation/docker.md). On shutdown the processor drains its delivery queues, so give it about 10 seconds before a hard kill — see [Running as a Service](../installation/manual.md#running-as-a-service).

### Logs

Logs go to stdout and, by default, to the `logs/` directory:

| Log File | Content |
|----------|---------|
| `processor.log` | Everything: startup, webhook processing, matching, delivery, bot commands, errors. Rotated by size (`[logging]`) |
| `webhooks.log` | Raw webhook bodies — only written if `[webhookLogging] enabled = true` |

Every 60 seconds (`[tuning] reload_interval_secs`) the processor logs a `[Status]` summary line — see [Monitoring](monitoring.md#the-status-line).

### Reloading

Tracking changes made through bot commands or the API trigger a debounced in-memory reload automatically (changes within 500 ms are coalesced). The processor also reloads from the database every `[tuning] reload_interval_secs` (default 60), which picks up changes made directly in MySQL.

To force a reload without restarting, use `!poracle-admin reload state` (tracking rules and humans), `!poracle-admin reload dts` (templates and partials) or `!poracle-admin reload geofence` (geofence files, including a Koji refresh). The equivalent API endpoints are `POST /api/reload`, `/api/dts/reload` and `/api/geofence/reload`.

Most `config.toml` changes need a restart. The [Poracle Config UI](../configuration/config-ui.md) tells you when a saved change requires one.
