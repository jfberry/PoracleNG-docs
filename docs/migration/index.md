# Migrating from PoracleJS

PoracleNG is a complete rewrite that splits Poracle into two components for dramatically better performance. This section covers what's changed and how to migrate your existing PoracleJS installation.

## What's Changed

| Aspect | PoracleJS | PoracleNG |
|--------|-----------|-----------|
| **Architecture** | Single Node.js process | Go processor + Node.js alerter |
| **Config format** | `default.json` + `local.json` | Single `config.toml` (TOML) |
| **Config naming** | camelCase | snake_case |
| **Scanner support** | MAD, RDM, Golbat | Golbat (default), RDM |
| **Webhook port** | 3031 | 3030 (processor) |
| **Data files** | `config/` inside alerter | `config/` at project root |
| **Logs** | `logs/` inside alerter | `logs/` at project root |
| **Build** | `npm install` | `make build` (Go + Node.js) |
| **Start** | `node server.js` | `./start.sh` (both components) |
| **Migrations** | Knex (Node.js) | golang-migrate (auto-adopts existing DBs) |

## Key Differences

### Two Components

PoracleNG runs as two processes:

1. **Processor** (Go, port 3030) — receives webhooks, matches against user rules in memory
2. **Alerter** (Node.js, port 3031) — renders templates, delivers messages, handles commands

The processor is where Golbat sends webhooks. It does all the heavy matching work in Go, then forwards only the matched results to the alerter for delivery.

### Config Format

The JSON config system (`default.json` merged with `local.json`) is replaced by a single TOML file at `config/config.toml`. All setting names use `snake_case` instead of `camelCase`.

### Scanner Support

MAD scanner support has been removed. Golbat is the default and recommended scanner. RDM is still supported by setting `scanner_type = "rdm"` under `[database]`.

### Database

The database schema is managed by golang-migrate instead of Knex. On first startup, the processor automatically detects and adopts existing PoracleJS databases — no manual migration needed.

### API Proxy

The processor reverse-proxies any `/api/` request it doesn't handle to the alerter. External tools like PoracleWeb only need the processor's address (port 3030).

## Next Steps

Follow the [step-by-step Migration Guide](guide.md) to convert your installation.
