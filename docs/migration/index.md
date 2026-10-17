# Migrating from PoracleJS

PoracleNG is a complete rewrite of Poracle in Go. It runs as a single binary that receives webhooks, matches them against tracking rules held in memory, renders alerts, and delivers them to Discord and Telegram — the same job PoracleJS did, with far better throughput. This section covers what's changed and how to migrate your existing PoracleJS installation.

!!! info "Already on PoracleNG?"
    This section is for moving from PoracleJS. To move between PoracleNG versions (for example 5.2 → 5.3), see [Upgrading & Release Notes](../operation/upgrading.md).

## What's Changed

| Aspect | PoracleJS | PoracleNG |
|--------|-----------|-----------|
| **Architecture** | Single Node.js process | Single Go binary (`processor/poracle-processor`) |
| **Runtime dependency** | Node.js | None — Go is only needed to build from source (Docker image is pre-built) |
| **Config format** | `default.json` + `local.json` | Single `config/config.toml` (TOML) |
| **Config naming** | camelCase | snake_case |
| **Scanner support** | MAD, RDM, Golbat | Golbat (default), RDM |
| **Webhook / API port** | `server.port` | `[processor] port` (default 3030; the migration script carries your old port across) |
| **Data files** | `config/` | `config/` at project root, with bundled defaults in `fallbacks/` |
| **Logs** | `logs/` (several files per day) | `logs/processor.log` (rotated by size) |
| **Build** | `npm install` | `make build` |
| **Start** | `npm start` | `./start.sh`, or PM2 with the bundled `ecosystem.config.js` |
| **Migrations** | Knex | golang-migrate (auto-adopts existing PoracleJS databases) |

## Key Differences

### One Process

Everything runs in one process on one port:

```
Golbat ──webhook──▶ PoracleNG (:3030) ──▶ Discord / Telegram
                         │
                         ▼
                       MySQL
```

Webhook ingestion, matching, DTS rendering, geocoding and static maps, delivery, bot commands, the REST API used by PoracleWeb, `/health` and Prometheus `/metrics` all live in the same binary on the same port.

!!! note "Early PoracleNG builds"
    The first PoracleNG releases paired a Go processor with a Node.js "alerter" on port 3031. That split is gone. If you still have `alerter_url`, `[alerter] port`, or a 3031 port mapping in your config or Compose file, they can be removed. An `api_secret` left under `[alerter]` is still read for backward compatibility and copied into `[processor]`, but new configs should set `[processor] api_secret` directly.

### Config Format

The JSON config system (`default.json` merged with `local.json`) is replaced by a single TOML file at `config/config.toml`. All setting names use `snake_case` instead of `camelCase`. The migration script converts your `local.json` for you.

### Scanner Support

MAD scanner support has been removed. Golbat is the default and recommended scanner. RDM is still supported by setting `type = "rdm"` under `[database.scanner]` (the older `scanner_type` under `[database]` is still accepted).

### Database

The database schema is managed by golang-migrate instead of Knex. On first startup, the processor automatically detects and adopts an existing PoracleJS database and then applies PoracleNG's own migrations — no manual migration needed. Take a backup first: some of those migrations change data in ways PoracleJS doesn't know about (see [Rollback](guide.md#rollback)).

## Next Steps

Follow the [step-by-step Migration Guide](guide.md) to convert your installation.
