# API

PoracleNG provides a REST API for external tools (e.g. PoracleWeb) to manage users, tracking, geofences, and configuration. The processor is a single binary — there is no separate alerter component, and the entire API is served from the processor on its configured port (default `3030`).

## Authentication

API access requires setting an `api_secret` in the `[processor]` section of `config.toml`:

```toml
[processor]
api_secret = "your-secret-key"
```

Requests must include the secret in the `X-Poracle-Secret` header:

```sh
curl -H "X-Poracle-Secret: your-secret-key" http://localhost:3030/api/stats/rarity
```

!!! note
    Leave `api_secret` blank (or unset) to disable the authenticated API entirely. The public endpoints (`POST /`, `GET /health`, `GET /metrics`) remain accessible.

!!! note "Backward compatibility"
    For installs migrating from older Poracle releases, an `api_secret` defined under `[alerter]` will still be read and copied into `[processor]` at load time. New configs should use `[processor]` directly.

## Public Endpoints

These do not require the `X-Poracle-Secret` header.

| Method | Path | Description |
|---|---|---|
| `POST` | `/` | Receive scanner webhooks (Golbat / RDM) |
| `GET` | `/health` | Health check |
| `GET` | `/metrics` | Prometheus metrics |
| `GET` | `/debug/pprof/*` | Go pprof endpoints (when enabled) |

## Authenticated Endpoints

All endpoints below sit under `/api/` and require `X-Poracle-Secret`.

### Reload

| Method | Path | Description |
|---|---|---|
| `POST` / `GET` | `/api/reload` | Reload state from the database |
| `POST` / `GET` | `/api/geofence/reload` | Reload geofences from disk |

### Data

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/weather` | Current weather state |
| `GET` | `/api/stats/rarity` | Pokemon rarity groups |
| `GET` | `/api/stats/shiny` | Shiny rate statistics |
| `GET` | `/api/stats/shiny-possible` | Pokemon observed as shiny |
| `GET` | `/api/geocode/forward` | Forward geocode lookup |
| `POST` | `/api/test` | Inject a synthetic webhook for template / delivery testing |

### Geofences

See [Geofence API](geofence.md) for full detail.

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/geofence/all` | Loaded geofences (raw) |
| `GET` | `/api/geofence/all/geojson` | Loaded geofences as GeoJSON |
| `GET` | `/api/geofence/all/hash` | Hash of current geofence set |
| `GET` | `/api/geofence/:area/map` | Static map for a named area |
| `GET` | `/api/geofence/locationMap/:lat/:lon` | Static map centred on a point |
| `GET` | `/api/geofence/distanceMap/:lat/:lon/:distance` | Static map with distance ring |
| `GET` | `/api/geofence/weatherMap/:lat/:lon` | Static map with weather overlay |
| `POST` | `/api/geofence/overviewMap` | Overview map (POST body) |

### Humans, tracking, profiles, config

These endpoints back PoracleWeb's user-facing functionality. See [Humans API](humans.md) and [Config API](config.md) for detail.

| Path prefix | Description |
|---|---|
| `/api/humans/*` | User CRUD |
| `/api/tracking/*` | Per-user tracking rules |
| `/api/profiles/*` | Profile CRUD |
| `/api/config/*` | Configuration queries used by PoracleWeb |
