# Migration Guide

This guide walks you through migrating an existing PoracleJS installation to PoracleNG.

## Prerequisites

- Go 1.25+ for 5.2.x (`main`) or Go 1.27+ for 5.3 (`develop`), plus `bash` and `git` — or use [Docker](../installation/docker.md) instead
- Node.js 18+ — **only needed to run the migration script**. PoracleNG itself has no Node.js runtime dependency.
- Your existing PoracleJS installation accessible on the filesystem
- Your existing database (will be auto-adopted) — **and a fresh backup of it** (`mysqldump`)

## Step 1: Clone and Build PoracleNG

```sh
git clone https://github.com/jfberry/PoracleNG.git
cd PoracleNG
make build
```

This produces a single binary at `processor/poracle-processor`. There is no separate alerter component in PoracleNG — all webhook handling, matching, rendering, and Discord/Telegram delivery happens inside the one binary.

!!! danger "Back up the database before first start"
    PoracleNG adopts your PoracleJS database in place and applies its own migrations on first startup. Several of these change data, and one (000009, in 5.3) is irreversible. A `mysqldump` taken now is your only clean way back to PoracleJS.

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
    The migration script moves PoracleJS's `server.apiSecret` to `[processor] api_secret`. Early PoracleNG configs kept it under `[alerter]`; PoracleNG still reads that location for backward compatibility and copies the value into `[processor]` at load time, but new configs should put it directly under `[processor]`.

!!! warning "Migrated before PoracleNG 5.1?"
    Two migration-script bugs were fixed in 5.1. If you converted your config with an older copy of the script, check by hand:

    - **`[general.dts_dictionary]`** — the old script snake-cased your custom keys (`scannerUrl` became `scanner_url`), so `{{scannerUrl}}` and friends render empty. Rename the keys back to exactly what your templates use.
    - **`[processor] host`** — the old script dropped PoracleJS's `server.host`. If you had bound the API to a specific address, set `host` again.

For a full walkthrough of every section, see the [Config File Reference](../configuration/config_file.md).

## Step 4: Update Webhook URL

Point your scanner (Golbat or RDM) at PoracleNG's `[processor]` `host:port`:

```
http://<your-host>:3030/
```

The migration script copies your PoracleJS `server.port` into `[processor] port`, so if PoracleNG runs on the same host as PoracleJS did, the webhook URL may not need to change. If you used a different port, or are moving hosts, update the scanner accordingly.

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

Then, as an admin, test that alerts render and deliver by sending `!poracle-test pokemon` in Discord (or `/poracle-test pokemon` in Telegram).

## Step 7: Update External Tools

If you use a web UI or other API clients, point them at the processor (port 3030) and set their API secret to match `[processor] api_secret`. The processor serves `/api/*` endpoints directly — there is no longer an alerter to proxy through.

## What About My Database?

Your existing PoracleJS database is adopted in place. On first startup, PoracleNG's migration tool detects the existing Knex-managed schema, creates a `schema_migrations` table marked at version 1, drops PoracleJS's foreign-key constraints, and then runs the remaining PoracleNG migrations. No manual database work is needed.

All existing user registrations, tracking rules, profiles, and areas are preserved. Users who were on the legacy "profile 0" are moved to profile 1 by migration 000009 in 5.3 — see [Upgrading & Release Notes](../operation/upgrading.md#migration-000009-profile-0-profile-1).

## Rollback

If you need to roll back to PoracleJS:

1. Stop PoracleNG (`Ctrl-C` or via your process manager)
2. **Restore the database backup** you took before the first PoracleNG start
3. Point your scanner webhooks back to PoracleJS's port
4. Start PoracleJS as before

!!! warning "The database is not left unchanged"
    PoracleNG's migrations go well beyond bookkeeping. Besides `schema_migrations` and dropping the foreign keys, they add tables (`maxbattle` if missing, `summary_schedules`, `user_locations`), add columns (per-rule location/area overrides, `pvp_ranking_evolution`, `costume` on `monsters` and `raid`), drop the legacy `invasion`/`lures` unique keys, and in 5.3 rewrite profile data (000009). PoracleNG has no downgrade command, and 000009 cannot be reversed at all. Rules created under PoracleNG may also use features PoracleJS doesn't understand.

    Pointing PoracleJS at the migrated database may appear to work, but it is untested and unsupported. Restore your backup instead — any tracking changes made while on PoracleNG will be lost.

## Troubleshooting

### Processor won't start

- Check your Go version: `go version` — 1.25+ for 5.2.x, 1.27+ for 5.3 (`develop`)
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
