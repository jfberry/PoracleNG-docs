# Migration Guide

This guide walks you through migrating an existing PoracleJS installation to PoracleNG.

## Prerequisites

- Go 1.25+
- Node.js 18+ — **only needed to run the migration script**. PoracleNG itself has no Node.js runtime dependency.
- Your existing PoracleJS installation accessible on the filesystem
- Your existing database (will be auto-adopted)

## Step 1: Clone and Build PoracleNG

```sh
git clone https://github.com/jfberry/PoracleNG.git
cd PoracleNG
make build
```

This produces a single binary at `processor/poracle-processor`. There is no separate alerter component in PoracleNG — all webhook handling, matching, rendering, and Discord/Telegram delivery happens inside the one binary.

## Step 2: Run the Migration Script

PoracleNG includes an automated migration script that converts your PoracleJS configuration:

```sh
node scripts/migrate-from-poracle.js
```

The script will:

1. **Ask** for the path to your existing PoracleJS installation
2. **Copy customized data files** (DTS templates, pokemon aliases, geofences, partials, etc.) into `config/`, skipping any that are unchanged from PoracleJS defaults
3. **Convert your `local.json`** overrides into the unified `config/config.toml`, stripping obsolete settings
4. **Print instructions** for any remaining manual steps

!!! tip
    The migration script only copies files you've actually customized. Files that match PoracleJS defaults are skipped — PoracleNG's built-in fallbacks in `fallbacks/` will be used instead.

!!! note "Docker users"
    If you run PoracleNG in Docker, you don't need Node.js installed on the host. Run the migration script in a throwaway Node container, mounting your PoracleJS and PoracleNG directories, then discard the container. See `README.md` in the PoracleNG repository for the exact command.

## Step 3: Review the Generated Config

Open `config/config.toml` and verify the converted settings. Pay special attention to:

```toml
[processor]
host = "0.0.0.0"
port = 3030
api_secret = "your-secret-key"

[database]
host = "127.0.0.1"
user = "poracleuser"
password = "poraclepassword"
database = "poracle"
```

!!! warning "Scanner Type"
    If you used RDM with PoracleJS, configure the scanner DB and set `type = "rdm"` under `[database.scanner]`. MAD is no longer supported — you'll need to switch to Golbat or RDM.

    ```toml
    [database.scanner]
    host = "127.0.0.1"
    port = 3306
    user = "scanneruser"
    password = "scannerpassword"
    database = "scannerdb"
    type = "rdm"          # "golbat" (default) or "rdm"
    ```

!!! note "api_secret location"
    In PoracleJS the API secret lived under `[alerter]`. PoracleNG reads that location for backward compatibility and copies the value into `[processor]` at load time, but new configs should put it directly under `[processor]`.

For a full walkthrough of every section, see the [Config File Reference](../configuration/config_file.md).

## Step 4: Update Webhook URL

Update your scanner (Golbat or RDM) to send webhooks to the PoracleNG processor on port **3030**:

```
http://<your-host>:3030/
```

In PoracleJS this was typically port `4201` (or whatever port you had configured). Whatever your previous address was, change it to the processor's `host:port` from `[processor]` in `config.toml`.

See [Scanner Setup](../installation/scanner.md) for Golbat/RDM webhook configuration.

## Step 5: Start PoracleNG

```sh
./start.sh
```

The start script verifies that `config/config.toml` exists, builds the processor if it's missing, then runs the binary. For production, use the included PM2 ecosystem file (see [Manual Install → PM2](../installation/manual.md)) so the processor gets the 10-second kill timeout it needs to drain its Discord/Telegram delivery queues on shutdown.

!!! note "First Startup"
    On first startup, the processor downloads game data (pokemon names, moves, locales) into `resources/`, loads all tracking data from your existing database, and begins accepting webhooks. Rarity and shiny stats will be empty until enough pokemon sightings accumulate.

## Step 6: Verify

```sh
curl http://localhost:3030/health
```

A `200 OK` response means the processor is up. Only one port and one health check — the old alerter port no longer exists.

Then test that alerts are working by sending `!poracle-test pokemon` in Discord (or `/poracle-test pokemon` in Telegram).

## Step 7: Update External Tools

If you use a web UI or other API clients, point them at the processor (port 3030) and set their API secret to match `[processor] api_secret`. The processor serves `/api/*` endpoints directly — there is no longer an alerter to proxy through.

## What About My Database?

Your existing PoracleJS database is fully compatible. On first startup, PoracleNG's migration tool detects the existing Knex-managed schema, marks the initial version as applied, and then runs any pending migrations. No manual database migration is needed.

All existing user registrations, tracking rules, profiles, and areas are preserved.

## Rollback

If you need to roll back to PoracleJS:

1. Stop PoracleNG (`Ctrl-C` or via your process manager)
2. Point your scanner webhooks back to PoracleJS's port
3. Start PoracleJS as before

Your database is unchanged — both versions can use it. PoracleNG's migrations only add a `schema_migrations` bookkeeping table and drop PoracleJS's foreign-key constraints; PoracleJS tolerates both.

## Troubleshooting

### Processor won't start

- Check that Go 1.25+ is installed: `go version`
- Check `logs/processor.log` for errors
- Verify database credentials in `config/config.toml`
- Confirm `config/config.toml` exists (the start script refuses to run without it)

### No alerts being sent

- Verify your scanner is sending webhooks to port **3030** (the processor)
- Check the processor health: `curl http://localhost:3030/health`
- Look at `logs/processor.log` for webhook processing and delivery errors
- If you use `api_secret`, confirm external clients (web UIs, tests) are sending the `X-Poracle-Secret` header with the matching value

### Config conversion issues

If the migration script couldn't convert a setting, it prints a warning. Refer to the [Config File Reference](../configuration/config_file.md) and `config/config.example.toml` for the correct TOML syntax, or use the [Poracle Config UI](../configuration/config-ui.md) to edit your config visually with validation.

### Need help?

Installation and migration help is available in the Poracle community Discord — see the link in PoracleNG's `README.md`. This channel is for operators; end-user tracking support is handled separately.
