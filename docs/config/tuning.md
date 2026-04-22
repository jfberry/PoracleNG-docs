# `[tuning]`

Performance and concurrency tuning for the processor, tileserver pipeline, and alerter.

!!! warning
    Do not change these settings without talking to us in Discord first. Bad values can cause queue starvation, rate-limit lockouts, or memory blowups. The defaults are reasonable for most installs.

## Processor

| Field | Type | Default | Description |
|---|---|---|---|
| `reload_interval_secs` | int | `60` | How often the processor reloads config and cached state. |
| `encounter_cache_ttl` | int | `3600` | TTL (seconds) for the encounter de-duplication cache. |
| `worker_pool_size` | int | `4` | Number of webhook processing workers. |
| `batch_size` | int | `50` | Max items processed per batch. |
| `flush_interval_millis` | int | `100` | How often pending batches are flushed. |

## Tileserver

Tiles are generated asynchronously: webhook handlers submit tile requests to a queue, dedicated workers POST them to the tileserver, and webhook payloads are held in the sender until the tile resolves or the deadline expires (in which case the fallback URL is used). This prevents a slow tileserver from blocking webhook processing.

| Field | Type | Default | Description |
|---|---|---|---|
| `tileserver_concurrency` | int | `2` | Tile worker goroutines (parallel POSTs to the tileserver). |
| `tileserver_timeout` | int | `10000` | HTTP timeout per tile POST (ms). |
| `tileserver_queue_size` | int | `100` | Max pending tile requests. Overflow uses the fallback URL. |
| `tileserver_deadline` | int | `10000` | Max time (ms) a payload waits for its tile before falling back. |
| `tileserver_failure_threshold` | int | `5` | Consecutive failures before the circuit breaker opens. |
| `tileserver_cooldown_ms` | int | `30000` | How long the breaker stays open before retrying. |
| `render_pool_size` | int | `8` | Concurrent render goroutines. |
| `render_queue_size` | int | `100` | Max buffered render jobs. |
| `delivery_queue_size` | int | `200` | Max buffered delivery jobs. |

## Alerter

| Field | Type | Default | Description |
|---|---|---|---|
| `max_database_connections` | int | `15` | Max database connections per worker. |
| `concurrent_matched_processors` | int | `10` | Pre-matched payloads processed concurrently. |
| `matched_max_queue_size` | int | `5000` | Max queued matched items before backpressure kicks in. |
| `concurrent_discord_destinations` | int | `10` | Concurrent Discord DM/channel sends per bot. |
| `concurrent_telegram_destinations` | int | `10` | Concurrent Telegram sends per bot. |
| `concurrent_discord_webhooks` | int | `10` | Concurrent Discord webhook sends. |

For outbound delivery, lower concurrency reduces the chance of hitting global rate limits.
