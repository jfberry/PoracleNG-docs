# API

PoracleNG exposes an HTTP API for external tools — PoracleWeb, ReactMap, the [Poracle Config UI](../configuration/config-ui.md), and custom integrations — to manage users and tracking, edit DTS templates and configuration, read game data, and inject messages. The processor is a single binary; the whole API is served by the processor on its configured `[processor] port` (default `3030`).

!!! tip "Live, exhaustive reference: `/docs` and `/openapi.json`"
    Every running processor serves an **OpenAPI 3.1** document at `GET /openapi.json` and interactive documentation at `GET /docs`. Both are generated from the code, so they always match the version you are running, and they list every field, enum, default and error code. These pages explain how the API fits together and give working examples; use `/docs` on your own server for field-level detail.

    ```sh
    curl http://localhost:3030/openapi.json -o poracle-openapi.json
    # or open http://localhost:3030/docs in a browser
    ```

## Authentication

Set an `api_secret` in the `[processor]` section of `config.toml`:

```toml
[processor]
api_secret = "your-secret-key"
```

Every request under `/api/` must then carry the secret in the `X-Poracle-Secret` header:

```sh
curl -H "X-Poracle-Secret: your-secret-key" http://localhost:3030/api/config/poracleWeb
```

A missing or wrong secret returns `401`:

```json
{"status": "authError", "reason": "incorrect or missing api secret"}
```

!!! danger "A blank `api_secret` leaves `/api` open"
    If `api_secret` is empty or unset, the authentication check is skipped and **every `/api/*` endpoint is reachable without a secret** — including user deletion, config rewrites, and message delivery. There is no startup warning. Always set a secret on any processor reachable from outside the host, and consider restricting access with `ip_whitelist` (below) or a firewall.

!!! note "Backward compatibility"
    An `api_secret` under `[alerter]` is still read and copied into `[processor]` at load time if `[processor] api_secret` is empty. New configs should use `[processor]`.

### IP allow/deny lists

`[processor] ip_whitelist` and `ip_blacklist` restrict which client IPs may connect. They apply to **every** route on the port — the API, `/health`, `/metrics`, and the scanner webhook receiver (`POST /`) — so if you set a whitelist, include your scanner's address. Rejected clients get a bare `403`.

```toml
[processor]
ip_whitelist = ["127.0.0.1", "10.0.0.5"]
```

### CORS

CORS headers (`Access-Control-Allow-Origin: *`) are sent on every response, and `OPTIONS` pre-flight requests are answered directly, so browser-based editors can call the API from another origin. The secret is still required.

## Public endpoints

These do **not** require `X-Poracle-Secret`.

| Method | Path | Description |
|---|---|---|
| `POST` | `/` | Scanner webhook receiver (Golbat / RDM) |
| `GET` | `/health` | Health check, version and capability flags |
| `GET` | `/metrics` | Prometheus metrics |
| `GET` | `/openapi.json` | OpenAPI 3.1 spec for the whole `/api` surface |
| `GET` | `/docs` | Interactive API documentation (Stoplight Elements, loaded from unpkg.com) |
| `GET` | `/debug/pprof/*` | Go pprof profiling endpoints |

!!! warning "pprof is always mounted"
    `/debug/pprof/` is registered unconditionally and is not behind the API secret. Don't expose the processor port directly to the internet; put it behind a reverse proxy that only forwards what you need, or use `ip_whitelist`.

### `GET /health`

```sh
curl http://localhost:3030/health
```

```json
{
  "status": "healthy",
  "version": "5.3.0",
  "capabilities": {
    "buttons": true,
    "snapshots": true,
    "autocreate": true,
    "tomlDts": true,
    "buttonResponseObject": true,
    "derivedDtsTypes": true,
    "costume": true,
    "customMaps": true
  }
}
```

`capabilities` lets clients detect features without probing endpoints. Treat a missing key as `false`. A capability means the binary *supports* a feature; it may still be switched off in config (for example buttons need `[snapshots] enabled = true`).

| Flag | Meaning |
|---|---|
| `buttons` | DTS entries can carry interactive `buttons[]`; `GET /api/dts/actions` exists |
| `snapshots` | Per-delivery snapshot store is present (`GET /api/snapshots/{messageID}`) |
| `autocreate` | Channel auto-create and `/api/autocreate/*` are present |
| `tomlDts` | `config/dts/*.toml` files are loaded and saved back in TOML |
| `buttonResponseObject` | Button `response_template_inline` accepts a JSON object |
| `derivedDtsTypes` | `/api/dts/testdata` and `/api/dts/enrich` understand DTS type names (`monsterChanged`, `incident`, `questSummary`, …) |
| `costume` | Pokemon and raid tracking rules accept a `costume` filter (5.3+) |
| `customMaps` | `GET /api/dts/custommaps` exists and `{{map}}` follows PoracleJS semantics (5.3+) |

## Errors

Most of the API is served through [huma](https://huma.rocks), and errors from those endpoints use **RFC 9457 `application/problem+json`**:

```json
{
  "title": "Unprocessable Entity",
  "status": 422,
  "detail": "validation failed",
  "errors": [
    {"message": "expected integer", "location": "body[0].min_iv", "value": "ninety"}
  ]
}
```

- `422` means the request failed schema validation (wrong type, missing required field, unknown field on a strict endpoint).
- `400` is used for handler-level validation on some older endpoints.
- `401` authentication failures keep the legacy `{"status":"authError"}` body shown above, because they're rejected before huma runs.
- The frozen **v1** endpoints (`/api/tracking/*`, `/api/humans/*`, `/api/profiles/*`) still return the old `{"status":"error","message":"…"}` shape. See [Legacy v1 API](humans.md).

Successful responses fall into three shapes:

- **Action endpoints** (reloads, enable/disable, deletes, …) return `{"status": "ok"}`.
- **Reads on the original `/api/*` endpoints** return `{"status": "ok", …data}`.
- **`/api/v2` reads and tracking mutations** return the resource or diff directly, with no `status` envelope.

## v1 or v2?

User, tracking and profile management exists in two versions:

- **`/api/v2`** — strict, typed and fully documented in `/docs`. Unknown fields are rejected, "any" is expressed by omitting a field rather than sending a magic number, and errors are problem+json. **Use v2 for anything new.** Newer features (the `incident` tracking type, saved-location update, mutes, `ping`, user listing and deletion) are v2-only.
- **v1** (`/api/tracking/*`, `/api/humans/*`, `/api/profiles/*`) — the original, lenient API. It's **frozen and deprecated but still supported**: it keeps working, gets no new features, and has no removal date yet.

See [Migrating from v1](v2/migration.md) for an endpoint-by-endpoint mapping.

All other endpoint groups (config, DTS, geofences, summaries, delivery, …) have only one version and live under `/api/`.

## Endpoint groups

| Group | Paths | Page |
|---|---|---|
| v2 tracking | `/api/v2/humans/{id}/tracking/*` | [Tracking (v2)](v2/tracking.md) |
| v2 humans & locations | `/api/v2/humans`, `/api/v2/humans/{id}/*` | [Humans & locations (v2)](v2/humans.md) |
| v2 profiles, roles & mutes | `/api/v2/humans/{id}/profiles`, `…/roles`, `…/mutes` | [Profiles, roles & mutes (v2)](v2/profiles.md) |
| Legacy v1 | `/api/tracking/*`, `/api/humans/*`, `/api/profiles/*` | [Legacy v1 API](humans.md) |
| Config & game data | `/api/config/*`, `/api/masterdata/*`, `/api/v2/activity`, `/api/weather`, `/api/stats/*`, `/api/geocode/*` | [Config & game data](config.md) |
| DTS editor | `/api/dts/*`, `/api/config/templates` | [DTS editor API](dts.md) |
| Geofences & map tiles | `/api/geofence/*` | [Geofence API](geofence.md) |
| Summaries | `/api/summaries/*` | [Summaries API](summaries.md) |
| Delivery, test & commands | `/api/deliverMessages`, `/api/test`, `/api/command`, `/api/resolve`, `/api/snapshots/*` | [Delivery, test & commands](messaging.md) |
| Auto-create | `/api/autocreate/*` | [Auto-create API](autocreate.md) |
| Reload | `/api/reload`, `/api/geofence/reload`, `/api/dts/reload` | [below](#reload-endpoints) |
| Validation hook (outbound) | Your URL, called by Poracle | [Validation hook](validation-hook.md) |

## Reload endpoints

All three accept `GET` or `POST` and return `{"status": "ok"}` when done.

| Endpoint | What it reloads |
|---|---|
| `/api/reload` | Humans and every tracking rule from the database. Geofences and DTS are left as they are. |
| `/api/geofence/reload` | Geofence files from disk **and** Koji, rebuilds the spatial index, then reloads humans and tracking from the database. Use after editing fences or rotating a Koji token. |
| `/api/dts/reload` | DTS templates and partials from `config/dts.json`, `config/dts/` and the bundled fallbacks. Doesn't touch tracking or geofences. Only registered when DTS rendering is available. |

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/geofence/reload
```

Tracking and human changes made through the API trigger a debounced reload automatically, so you only need `/api/reload` after editing the database directly.

## Upstream reference

The PoracleNG repository also has [`API.md`](https://github.com/jfberry/PoracleNG/blob/main/API.md) and the [v1 → v2 migration guide](https://github.com/jfberry/PoracleNG/blob/main/docs/v1-to-v2-migration-guide.md). Where those disagree with `/openapi.json` from your running processor, the OpenAPI spec is correct.

!!! info "Version notes"
    The OpenAPI spec, `/api/v2` and v2 mutes are in 5.2. Items marked **5.3+** on these pages are on the `develop` branch and will ship in the next release.
