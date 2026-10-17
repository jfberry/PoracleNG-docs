# `[processor]`

Network settings for the PoracleNG HTTP server. PoracleNG is a single binary, and this one listener receives scanner webhooks and serves the API.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `host` | string | `"127.0.0.1"` | Listen address. `127.0.0.1` = this machine only; `0.0.0.0` = all interfaces. If unset, the `PORACLE_HOST` environment variable is used when present (the Docker image sets it to `0.0.0.0`). A value in `config.toml` always wins. |
| `port` | int | `3030` | Listen port. |
| `ip_whitelist` | array(string) | `[]` | If non-empty, only these client IPs may connect, on **every** route, including the webhook receiver. 5.3+: exact IPs or CIDR ranges, IPv4 or IPv6 (e.g. `"10.0.0.0/8"`, `"2001:db8::/32"`). In 5.2.x, exact IPs only. |
| `ip_blacklist` | array(string) | `[]` | Client IPs that are refused on every route. Checked before the whitelist. Same format as `ip_whitelist`. |
| `trusted_proxies` | array(string) | `[]` | **5.3+.** Reverse proxies (IPs or CIDR ranges) whose `X-Forwarded-For` / `X-Real-IP` headers are believed when working out the client IP. Empty = trust no proxy: the client IP is always the TCP peer address. |
| `api_secret` | string | `""` | Shared secret required in the `X-Poracle-Secret` header for `/api/*` (and `/debug/pprof` when enabled). **Empty = no authentication**: anyone who can reach the port has full API access. |
| `enable_pprof` | bool | `false` | **5.3+.** Serve Go profiling handlers at `/debug/pprof`, behind the same `api_secret` as `/api`. Leave off unless you are diagnosing a performance problem. |

```toml
[processor]
host = "127.0.0.1"
port = 3030
ip_whitelist = []                  # e.g. ["192.168.1.0/24", "203.0.113.7"]
ip_blacklist = []
trusted_proxies = []               # 5.3+: e.g. ["172.16.0.0/12"] behind a reverse proxy
api_secret = "a-long-random-string"
enable_pprof = false               # 5.3+
```

!!! info "PoracleNG 5.3+"
    Invalid entries in `ip_whitelist`, `ip_blacklist` or `trusted_proxies` **stop startup** with an error naming the bad entry. In 5.2.x they were silently ignored.

!!! danger "Always set `api_secret`"
    With `api_secret` blank, `/api/*` is **open with no authentication**. Anyone who can reach the port can read and change users, tracking rules, config and templates, and send messages through your bots. Set a long random secret, and don't expose the port to the internet unless you have to.

    From 5.3, startup logs a warning when `api_secret` is blank **and** the effective listen host isn't loopback (`127.0.0.1`, `::1`, `localhost`). The effective host includes the `PORACLE_HOST` override, so the Docker image's `0.0.0.0` triggers it. A blank secret on a loopback host is still allowed without a warning.

!!! warning "Behind a reverse proxy? Set `trusted_proxies` (5.3+)"
    From 5.3, forwarded headers are ignored unless the request comes from an address in `trusted_proxies`. If Poracle sits behind nginx, Traefik or a Docker network and you rely on `ip_whitelist` / `ip_blacklist`, add the proxy's address or range, for example `trusted_proxies = ["172.16.0.0/12"]`. Otherwise every request is seen as coming from the proxy, so a whitelist either blocks everything or lets everything through the proxy.

    In 5.2.x every proxy was trusted, so any client could set `X-Forwarded-For` itself and bypass the IP lists. On 5.2.x, don't rely on the IP lists to protect the API: use `api_secret` and a firewall.

!!! note
    If you set `ip_whitelist`, remember to include your scanner's (Golbat's) IP, or webhooks are rejected too.

## Routes

| Route | Auth |
|---|---|
| `POST /` | None (webhook receiver) |
| `GET /health` | None |
| `GET /metrics` | None (Prometheus) |
| `/debug/pprof/*` | 5.3+: only exists when `enable_pprof = true`, and needs `X-Poracle-Secret` when `api_secret` is set. In 5.2.x it is always served, with no authentication. |
| `/api/*` | `X-Poracle-Secret` when `api_secret` is set |

All routes are subject to `ip_whitelist` / `ip_blacklist`.

!!! info "PoracleNG 5.3+: pprof is off by default"
    If you used `/debug/pprof`, set `enable_pprof = true` and send the `X-Poracle-Secret` header, for example `curl -H "X-Poracle-Secret: <secret>" -o cpu.pprof "http://127.0.0.1:3030/debug/pprof/profile?seconds=30"`, then `go tool pprof cpu.pprof`. Startup warns if `enable_pprof` is on with a blank `api_secret`.

!!! note "Legacy `[alerter]` section"
    Older configs that set `api_secret` under `[alerter]` still work: it is used when `[processor] api_secret` is empty. Move it to `[processor]`.

See also: [HTTP API](../api/index.md).
