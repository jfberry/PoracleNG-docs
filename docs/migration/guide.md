# Migration Guide

This guide walks you through migrating an existing PoracleJS installation to PoracleNG.

## Prerequisites

- Go 1.21+
- Node.js 18+
- Your existing PoracleJS installation accessible on the filesystem
- Your existing database (will be auto-adopted)

## Step 1: Clone and Build PoracleNG

```sh
git clone https://github.com/jfberry/PoracleNG.git
cd PoracleNG
make build
```

## Step 2: Run the Migration Script

PoracleNG includes an automated migration script that converts your PoracleJS configuration:

```sh
node scripts/migrate-from-poracle.js
```

The script will:

1. **Ask** for the path to your existing PoracleJS installation
2. **Copy customized data files** (DTS templates, aliases, geofences, etc.) into `config/`, skipping any that are unchanged from Poracle's defaults
3. **Convert your `local.json`** overrides into the unified `config/config.toml`, stripping obsolete settings
4. **Print instructions** for any manual steps needed

!!! tip
    The migration script only copies files you've actually customized. Files that match PoracleJS defaults are skipped — PoracleNG's built-in fallbacks in `fallbacks/` will be used instead.

## Step 3: Review the Generated Config

Open `config/config.toml` and verify the converted settings. Pay special attention to:

```toml
[processor]
host = "0.0.0.0"
port = 3030
alerter_url = "http://localhost:3031"

[alerter]
host = "127.0.0.1"
port = 3031
processor_url = "http://localhost:3030"

[database]
host = "127.0.0.1"
user = "poracleuser"
password = "poraclepassword"
database = "poracle"
```

!!! warning "Scanner Type"
    If you used RDM with PoracleJS, add `scanner_type = "rdm"` under `[database]`. MAD is no longer supported — you'll need to switch to Golbat.

## Step 4: Update Webhook URL

Update your scanner (Golbat) to send webhooks to the **processor** instead of the old alerter:

| Before (PoracleJS) | After (PoracleNG) |
|--------------------|--------------------|
| `http://host:3031/` | `http://host:3030/` |

The processor now receives all webhooks on port 3030 and handles matching internally.

## Step 5: Start PoracleNG

```sh
./start.sh
```

The start script will:

1. Build the processor and install alerter dependencies if needed
2. Start the processor and wait for it to pass a health check
3. Start the alerter
4. Monitor both processes and shut them down together on Ctrl-C

!!! note "First Startup"
    On first startup, the processor downloads game data (pokemon names, moves, locales) into `resources/`, loads all tracking data from the database, and begins accepting webhooks. Rarity and shiny stats will be empty until enough pokemon sightings accumulate.

## Step 6: Verify

Check that both components are healthy:

```sh
# Processor health check
curl http://localhost:3030/health

# Alerter health check
curl http://localhost:3031/health
```

Test that alerts are working by sending a `!poracle-test pokemon` command in Discord (or `/test pokemon` in Telegram).

## Step 7: Update External Tools

If you use PoracleWeb or other API clients, point them at the **processor** (port 3030). The processor reverse-proxies all `/api/` requests to the alerter, so external tools only need one URL.

## What About My Database?

Your existing PoracleJS database is fully compatible. On first startup, golang-migrate detects the existing schema and adopts it. No manual database migration is needed.

All existing user registrations, tracking rules, profiles, and areas are preserved.

## Rollback

If you need to roll back to PoracleJS:

1. Stop PoracleNG (`Ctrl-C` or kill both processes)
2. Point your scanner webhooks back to port 3031
3. Start PoracleJS as before

Your database is unchanged — both versions can use it.

## Troubleshooting

### Processor won't start

- Check that Go 1.21+ is installed: `go version`
- Check `logs/processor.log` for errors
- Verify database credentials in `config/config.toml`

### Alerter won't start

- Check that Node.js 18+ is installed: `node --version`
- Run `cd alerter && npm install` if dependencies are missing
- Check `logs/general-*.log` for errors

### No alerts being sent

- Verify Golbat is sending webhooks to port **3030** (processor), not 3031
- Check the processor health: `curl http://localhost:3030/health`
- Check `logs/processor.log` for webhook processing errors
- Verify the alerter is receiving matched results in `logs/general-*.log`

### Config conversion issues

If the migration script couldn't convert a setting, it will print a warning. Refer to the [Config File Reference](../configuration/config_file.md) and `config/config.example.toml` for the correct TOML syntax.
