# Web UI Installation

PoracleWeb and PoracleWeb.NET are separate projects with their own build and deployment processes. This page covers how to configure each to talk to your PoracleNG instance. For host-side setup (runtime, dependencies, web server), follow the upstream READMEs — we don't duplicate those here because they change over time.

## Prerequisites (either option)

- PoracleNG running with `api_secret` configured in `[processor]` and its API port reachable from wherever the UI is hosted

```toml
# On the PoracleNG side — config/config.toml
[processor]
host = "0.0.0.0"
port = 3030
api_secret = "a-long-random-string"
```

Both UIs authenticate by sending the `X-Poracle-Secret` header. Whichever UI you pick needs two values in its own config: the API URL and this secret.

## PoracleWeb (Node.js / React)

Upstream: [WatWowMap/PoracleWeb](https://github.com/WatWowMap/PoracleWeb)

### Install

```sh
git clone https://github.com/WatWowMap/PoracleWeb.git
cd PoracleWeb
npm install
```

### Configure

```sh
cp config.example.json config.json
```

Edit `config.json` and set:

- **API URL** — the processor address (e.g. `http://localhost:3030`)
- **API Secret** — must match `api_secret` in your PoracleNG `config.toml`
- **Map settings** — center coordinates, zoom level, tile provider

### Run

Development server:

```sh
npm start
```

Production build:

```sh
npm run build
```

The `build/` directory can be served by Nginx, Apache, or any static file server.

## PoracleWeb.NET (ASP.NET)

Upstream: [PGAN-Dev/PoracleWeb.NET](https://github.com/PGAN-Dev/PoracleWeb.NET)

### Install

Follow the build and deployment instructions in the upstream repository — these depend on your .NET runtime and hosting choice and are maintained there.

### Configure

However PoracleWeb.NET's configuration is structured in the version you installed, you need to provide:

- **API URL** — the processor address (e.g. `http://localhost:3030`)
- **API Secret** — must match `api_secret` in your PoracleNG `config.toml`

### Run

Follow the upstream README for the run command appropriate to your deployment (self-contained binary, IIS, Docker, etc.).

## Exposing the API safely

Whichever UI you use, it needs to reach the PoracleNG processor over HTTP. Options, in rough order of preference:

1. **Same host** — UI and PoracleNG on the same server, talking over `localhost:3030`
2. **Private network** — both on an internal network (VPN, WireGuard, VPC) with the processor port bound to the internal interface
3. **Reverse proxy with IP allow-list** — front the processor with Nginx or Caddy, restrict the `/api` path to known IPs

Binding `[processor] host = "0.0.0.0"` with no further restriction exposes the API to anyone who can reach the port — treat `api_secret` as a credential, not a firewall.

## Troubleshooting

### The web UI can't connect

- Confirm the API URL is correct and reachable: `curl http://<processor-host>:3030/health` from wherever the UI is running
- Confirm the API secret matches — a mismatch returns HTTP 401; the processor logs `unauthorized` lines in `logs/processor.log`
- If using a reverse proxy, make sure it forwards the `X-Poracle-Secret` header (some proxies strip unknown headers by default)

### Need help?

Setup and connectivity questions can be asked in the Poracle community Discord. UI-specific bugs should be filed against the relevant upstream repository:

- [WatWowMap/PoracleWeb issues](https://github.com/WatWowMap/PoracleWeb/issues)
- [PGAN-Dev/PoracleWeb.NET issues](https://github.com/PGAN-Dev/PoracleWeb.NET/issues)
