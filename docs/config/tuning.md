# `[tuning]`

Performance and concurrency tuning for webhook processing, static map tiles, geocoding, rendering, delivery and the validation hook.

!!! warning
    Do not change these settings without talking to us in Discord first. Bad values can cause queue starvation, rate-limit lockouts, or memory blowups. The defaults are reasonable for most installs.

## Processing

| Field | Type | Default | Description |
|---|---|---|---|
| `reload_interval_secs` | int | `60` | How often tracking rules, humans and profiles are reloaded from the database. Also how often the `[Status]` log line is written. |
| `worker_pool_size` | int | `4` | Max webhooks processed concurrently. |
| `render_pool_size` | int | `8` | Concurrent DTS render goroutines. |
| `render_queue_size` | int | `100` | Max buffered render jobs before backpressure. |

## Tileserver

Tiles are generated asynchronously: webhook handlers submit tile requests to a queue, dedicated workers POST them to the tileserver, and payloads wait until the tile is ready or the deadline passes (then the fallback image is used). A slow tileserver can't block webhook processing.

| Field | Type | Default | Description |
|---|---|---|---|
| `tileserver_concurrency` | int | `2` | Tile worker goroutines (parallel POSTs to the tileserver). |
| `tileserver_timeout` | int | `10000` | HTTP timeout per tile POST (ms). |
| `tileserver_queue_size` | int | `100` | Max pending tile requests. Overflow uses the fallback URL. |
| `tileserver_deadline` | int | `10000` | Max time (ms) a payload waits for its tile before falling back. |
| `tileserver_failure_threshold` | int | `5` | Consecutive failures before the tileserver circuit breaker opens. |
| `tileserver_cooldown_ms` | int | `30000` | How long the breaker stays open before retrying. |
| `tileserver_pregen_ttl` | int | `86400` | TTL (seconds) requested for pregenerated tiles. `-1` = send no TTL. Can be overridden per map type with [`tileserver_settings.<type>.ttl`](geocoding.md#geocodingtileserver_settingstype). |

## Geocoding

Used by both the reverse geocoder (`[geocoding] provider`) and GeoNames intersection lookups (`[geocoding] intersection_users`). Each has its **own** limiter and circuit breaker sized from these values, so total outbound geo traffic can peak at twice `geocoding_concurrency`.

| Field | Type | Default | Description |
|---|---|---|---|
| `geocoding_concurrency` | int | `5` | Max concurrent outbound requests per geocoding service. |
| `geocoding_timeout` | int | `5000` | HTTP timeout per request (ms). |
| `geocoding_failure_threshold` | int | `5` | Consecutive failures before the circuit breaker opens. |
| `geocoding_cooldown_ms` | int | `30000` | How long the breaker stays open before retrying (ms). |

`0` (or leaving the key out) uses the default.

## Delivery

| Field | Type | Default | Description |
|---|---|---|---|
| `delivery_queue_size` | int | `200` | Buffer size of **each destination's** delivery queue (one queue per user, channel or webhook with pending messages). Memory use grows with the number of active destinations. Idle queues are removed after 60 seconds. When a queue is full, the **oldest** queued alert is dropped (rate-limit and ban notices are never dropped). |
| `concurrent_discord_destinations` | int | `10` | Concurrent Discord DM/channel sends. |
| `concurrent_telegram_destinations` | int | `10` | Concurrent Telegram sends. |
| `concurrent_discord_webhooks` | int | `10` | Concurrent Discord webhook sends. |

For outbound delivery, lower concurrency reduces the chance of hitting global rate limits. See [Rate limits](../operation/ratelimits.md).

## Validation hook

Used only when [`[validation] url`](validation.md) is set.

| Field | Type | Default | Description |
|---|---|---|---|
| `validation_timeout_ms` | int | `1500` | Per-call HTTP timeout (ms). |
| `validation_max_concurrent` | int | `16` | Max parallel validator calls per webhook event. |

## Unused keys

`encounter_cache_ttl`, `batch_size` and `flush_interval_millis` are still accepted, but PoracleNG doesn't use them. `max_database_connections`, `concurrent_matched_processors` and `matched_max_queue_size` were removed with the old alerter and are ignored. All of these can be deleted from your config.
