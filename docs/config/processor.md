# `[processor]`

Network settings for the PoracleNG HTTP server. PoracleNG is a single binary, and this one listener receives scanner webhooks and serves the API.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `host` | string | `"127.0.0.1"` | Listen address. `127.0.0.1` = this machine only; `0.0.0.0` = all interfaces. If unset, the `PORACLE_HOST` environment variable is used when present (the Docker image sets it to `0.0.0.0`). A value in `config.toml` always wins. |
| `port` | int | `3030` | Listen port. |
| `ip_whitelist` | array(string) | `[]` | If non-empty, only these client IPs may connect, on **every** route, including the webhook receiver. Exact IP addresses only; CIDR ranges are not supported. |
| `ip_blacklist` | array(string) | `[]` | Client IPs that are refused on every route. Exact IP addresses only. |
| `api_secret` | string | `""` | Shared secret required in the `X-Poracle-Secret` header for `/api/*`. **Empty = no authentication**: anyone who can reach the port has full API access. |

```toml
[processor]
host = "127.0.0.1"
port = 3030
ip_whitelist = []
ip_blacklist = []
api_secret = "a-long-random-string"
```

!!! danger "Always set `api_secret`"
    With `api_secret` blank, `/api/*` is **open with no authentication**. Anyone who can reach the port can read and change users, tracking rules, config and templates, and send messages through your bots. Set a long random secret, and don't expose the port to the internet unless you have to.

!!! warning "IP filtering and proxies"
    The IP lists check the client address as reported by the HTTP server, which honours `X-Forwarded-For` / `X-Real-IP` headers. A client that can reach the port directly can set those headers itself, so don't rely on the IP lists alone to protect the API. Use `api_secret`, and firewall the port.

    If you set `ip_whitelist`, remember to include your scanner's (Golbat's) IP, or webhooks are rejected too.

## Routes

| Route | Auth |
|---|---|
| `POST /` | None (webhook receiver) |
| `GET /health` | None |
| `GET /metrics` | None (Prometheus) |
| `/debug/pprof/*` | None |
| `/api/*` | `X-Poracle-Secret` when `api_secret` is set |

All routes are subject to `ip_whitelist` / `ip_blacklist`.

!!! note "Legacy `[alerter]` section"
    Older configs that set `api_secret` under `[alerter]` still work: it is used when `[processor] api_secret` is empty. Move it to `[processor]`.

See also: [HTTP API](../api/index.md).
