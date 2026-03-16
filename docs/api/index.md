# API

PoracleNG provides a REST API for external tools like PoracleWeb to manage users, tracking, and configuration.

## Authentication

API access requires setting an `api_secret` in the alerter config:

```toml
[alerter]
api_secret = "your-secret-key"
```

Requests must include the secret in the `X-Poracle-Secret` header:

```sh
curl -H "X-Poracle-Secret: your-secret-key" http://localhost:3030/api/config/poracleWeb
```

!!! note
    Leave `api_secret` blank to disable API access entirely.

## API Proxy

The processor reverse-proxies all `/api/` requests it doesn't handle to the alerter. External tools only need to know the **processor's** address (default port 3030).

## Processor Endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | `/` | Receive scanner webhooks |
| POST | `/api/reload` | Trigger in-memory data reload |
| GET | `/api/weather` | Current weather state |
| GET | `/api/stats/rarity` | Pokemon rarity groups |
| GET | `/api/stats/shiny` | Shiny rate statistics |
| GET | `/api/stats/shiny-possible` | Pokemon observed as shiny |
| GET | `/health` | Health check |
| GET | `/metrics` | Prometheus metrics |
| Various | `/api/*` | Proxied to alerter |

## Alerter Endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/matched` | Receive pre-matched results from processor |
| Various | `/api/tracking/*` | User tracking management |
| Various | `/api/humans/*` | User management |
| Various | `/api/profiles/*` | Profile CRUD |
| GET | `/api/config/*` | Configuration queries |
| GET | `/api/masterdata/*` | Game master data |
| GET | `/api/geofence/*` | Geofence tile maps |
| POST | `/api/postMessage` | Direct message posting |
| GET | `/health` | Health check |
| GET | `/metrics` | Prometheus metrics |

## Connections

| From | To | Endpoint | Purpose |
|------|----|----------|---------|
| Scanner | Processor | `POST /` | Raw webhooks |
| Processor | Alerter | `POST /api/matched` | Pre-matched results |
| Alerter | Processor | `POST /api/reload` | Trigger reload |
| PoracleWeb | Processor | `/api/*` | User/tracking management (proxied) |
