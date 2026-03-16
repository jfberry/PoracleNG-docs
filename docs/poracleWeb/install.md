# PoracleWeb Installation

## Prerequisites

- PoracleNG running with `api_secret` configured
- Node.js 18+
- A web server (or use the built-in development server)

## Installation

```sh
git clone https://github.com/WatWowMap/PoracleWeb.git
cd PoracleWeb
npm install
```

## Configuration

Create a configuration file pointing to your PoracleNG processor:

```sh
cp config.example.json config.json
```

Edit `config.json` to set:

- **API URL** — the processor address (e.g. `http://localhost:3030`)
- **API Secret** — must match the `api_secret` in your PoracleNG config
- **Map settings** — center coordinates, zoom level, tile provider

## Running

### Development

```sh
npm start
```

### Production

Build the static files and serve with a web server:

```sh
npm run build
```

The `build/` directory can be served by Nginx, Apache, or any static file server.

## Connecting to PoracleNG

PoracleWeb communicates exclusively through the API. The processor reverse-proxies API requests to the alerter, so PoracleWeb only needs the processor's address.

```
PoracleWeb ──API──▶ Processor (:3030) ──proxy──▶ Alerter (:3031)
```

Make sure the `api_secret` in PoracleWeb's config matches the one in `config/config.toml`:

```toml
[alerter]
api_secret = "your-secret-key"
```
