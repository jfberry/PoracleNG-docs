# FAQ & Troubleshooting

## General

### How do I update PoracleNG?

Back up your database, then:

```sh
git pull
make build
pm2 restart poracle      # or ./start.sh / your service manager
```

Database migrations are applied automatically on startup. Read [Upgrading & Release Notes](upgrading.md) first, since some migrations can't be undone. Docker users run `docker compose pull && docker compose up -d`.

### Which version am I running?

Send `!version` to the bot. It shows the version (e.g. `5.3.0-develop`), commit and build date. `main` is 5.2.x; `develop` is 5.3.0-dev.

### Can I run PoracleNG and PoracleJS side by side?

Not on the same database. Moving from PoracleJS to PoracleNG changes the schema and data, so going back means restoring a backup. See [Migration Guide → Rollback](../migration/guide.md#rollback).

### Does PoracleNG support Docker?

Yes. Pre-built images are `ghcr.io/jfberry/poracleng:main` (stable, also `:latest`) and `ghcr.io/jfberry/poracleng:develop`. See the [Docker Installation](../installation/docker.md) guide.

## Startup Issues

### Processor won't start

- Building from source? Check `go version`: 1.25+ for 5.2.x, 1.27+ for `develop`/5.3. The required version is the `go` line in `processor/go.mod`.
- `make build` and `start.sh` need `bash` (they run `scripts/goldflags.sh`)
- Check `logs/processor.log`, or the console output, for the error
- Verify database credentials in `config/config.toml`
- Ensure MySQL/MariaDB is running and accessible

### "Port already in use" error

Another process is using port 3030 (often a leftover PoracleNG or PoracleJS). Stop it, or change the port:

```toml
[processor]
port = 3030
```

PoracleNG uses only this one port. If you have an `[alerter]` section with a `port`, it is a leftover from early releases and is ignored (only `[alerter] api_secret` is still read).

### Docker: the container is healthy but nothing can reach port 3030

`config.example.toml` sets `[processor] host = "127.0.0.1"`, which overrides the image's `PORACLE_HOST=0.0.0.0`. Set `host = "0.0.0.0"` in your `config.toml`. See [Docker → Listen Address](../installation/docker.md#listen-address).

## Alert Issues

### No alerts being sent

1. **Check webhooks are arriving** — the `[Status]` line in `logs/processor.log` shows `Webhooks: N/min`, and a `No webhooks have been received` warning means nothing arrived
2. **Verify scanner config** — webhooks must go to the processor's `[processor]` `host:port` (default 3030)
3. **Check user has tracking** — `!tracked` should show active subscriptions
4. **Check user has areas/location** — tracking needs at least one area, or a location with a distance
5. **Check bot permissions** — the Discord bot needs Send Messages permission in DMs or channels
6. **Check rate limits** — the destination may be limited or banned: `!poracle-admin ratelimit list` / `show <id>`. Stopped users re-enable with `!start`; admin-disabled ones need `!enable <id>`

### Alerts are delayed

- Look at `RenderQ` and `Lanes` in the `[Status]` line (see [Monitoring](monitoring.md#the-status-line))
- Check tileserver and geocoder latency (`Tiles` / `Geo` in the same line)
- Review `[tuning]` settings, especially the `concurrent_*` values
- Check whether Discord/Telegram rate limiting is throttling delivery (`RateLimited:` in the status line)

### What does "delivery shedding messages" / "lane full … shedding oldest queued messages" mean?

A single destination (a user, channel, webhook or Telegram chat) has `delivery_queue_size` alerts queued, nearly always because Discord or Telegram is rate-limiting it. From 5.3, PoracleNG drops that destination's **oldest** queued alert to make room for each new one, so other destinations aren't held up. The log line names the target.

The usual fix is to narrow that destination's tracking, or lower its limit with an `[[alert_limits.overrides]]` entry. Raising `[tuning] delivery_queue_size` only buys time and costs memory. See [Rate Limits → Delivery Lanes](ratelimits.md#delivery-lanes).

### A user or channel was "auto-disabled" — why?

Either:

- **Rate-limit ban** — it went over its limit `max_limits_before_stop` times (default 10) within 24 hours. The admin channel notice reads `Rate-limit ban: auto-disabled …`.
- **Delivery failures** — 10 sends in a row failed (bot blocked, channel deleted, webhook revoked). The notice reads `Delivery failure: auto-disabled …`.

Check with `!pa ratelimit show <id>`. Fix the cause, then re-enable with `!start` (run by the user or in the channel), or `!enable <id>` if `disable_on_stop = true`. `!pa ratelimit reset <id>` clears the breach history. Set `[discord] admin_channel_id` to get these notices. See [Rate Limits](ratelimits.md).

### Bot doesn't respond to commands

- Ensure **Message Content Intent** is enabled in the Discord Developer Portal
- Check the bot is online (not showing as offline in Discord)
- Verify the command prefix matches config (`!` by default)
- Check `logs/processor.log` for command errors. To log every command to a Discord channel, set `[discord] dm_log_channel_id`

### Test command works but real alerts don't

- `!poracle-test` (`/poracle-test` on Telegram) renders bundled sample data; it doesn't need webhooks
- Verify your scanner is sending webhooks to port 3030
- Check `logs/processor.log` for webhook processing activity

### PVP alerts never fire for level 51 (or another cap)

Look for this warning in the log:

```
WARN … PVP configuration mismatch: Golbat sent league 1500 ranks for level cap 51, which is not in [pvp] level_caps [50] - these ranks are ignored.
```

`[pvp] level_caps` must match the level caps set in Golbat's PVP config. Ranks for any other cap are ignored. The warning is logged once per cap and is new in 5.3. Earlier versions dropped those ranks without saying so.

## Configuration Issues

### Config changes not taking effect

- Most `config.toml` changes need a restart. The [Poracle Config UI](../configuration/config-ui.md) tells you when a saved change needs one.
- DTS templates and geofences can be reloaded live: `!poracle-admin reload dts`, `!poracle-admin reload geofence` (also re-fetches Koji). `!poracle-admin reload state` reloads tracking from the database.
- Verify syntax in `config.toml`. TOML is strict about quotes and types.
- Check setting names (all `snake_case`). In `[discord]`, put plain keys **before** any `[discord.xxx]` sub-tables, or TOML assigns them to the sub-table.

### How do I check what config the running bot actually loaded?

Use `!poracle-admin config`:

| Command | Shows |
|---------|-------|
| `!pa config` | The whole effective config (secrets masked) |
| `!pa config <section>` | One section, e.g. `!pa config alert_limits` |
| `!pa config search <term>` | Every key whose name or value contains the term, e.g. `search shlink`, `search 3030` |
| `!pa config keys` | Section and key names |

### `disable_pokestop` doesn't do anything

It is deprecated and does nothing. Use `disable_lure`, `disable_invasion`, `disable_quest` and/or `disable_showcase` instead. See [Scanner Setup](../installation/scanner.md#disabling-webhook-types).

### Geofences not loading

- Check file paths in `[geofence] paths` are correct relative to the project root
- Verify JSON syntax in geofence files
- Check `logs/processor.log` for geofence and `[KOJI]` messages
- Run `!poracle-admin reload geofence` after fixing files

## Database Issues

### Migration errors on startup

The processor uses golang-migrate for database schema management. If you see migration errors:

1. Check that the database user has `CREATE`, `ALTER`, `INDEX` and `DROP` permissions
2. Look at the specific error in `logs/processor.log`
3. If a migration was interrupted, the processor forces the recorded version and retries on the next start. If it keeps failing, restore your pre-upgrade backup and ask for help.

### After upgrading to 5.3, some users' rules disappeared or their profile changed

Migration 000009 moves everyone who was on the non-existent "profile 0" to profile 1. Their profile-0 rules are merged into profile 1, with one exception: users who had already switched away from profile 0 and rebuilt their rules on profile 1 had the abandoned profile-0 rules **deleted**. This can't be reversed except from a backup. See [Upgrading → Migration 000009](upgrading.md#migration-000009-profile-0-profile-1).

### High database load

- Check that `[tuning] reload_interval_secs` (default 60) isn't set too low
- Ensure database indexes are intact
- The processor's own connection pool is fixed (10 connections). `max_database_connections` in `config.example.toml` is a leftover and does nothing.

## Discord-Specific

### Bot shows as offline

- Verify the bot token is correct
- Check that the bot application hasn't been disabled
- Look for authentication errors in `logs/processor.log`

### Missing permissions

The bot needs: Send Messages, Manage Messages, Embed Links, Attach Files, Read Message History, Use External Emojis, Add Reactions. Add Manage Roles if using role subscriptions, and Manage Webhooks / Manage Channels for webhook and channel auto-creation.

### Rate limited by Discord

Discord enforces its own rate limits. If you're hitting them:

- Add more bot tokens as workers
- Reduce `concurrent_discord_destinations` in tuning
- Make sure users aren't tracking too broadly (`!pa ratelimit list`)

## Telegram-Specific

### Bot can't send messages to users

Users must click "Start" on the bot in a private chat before the bot can message them. This is a Telegram requirement. Repeated failures auto-disable the user after 10 consecutive failed sends.

### Messages not formatted correctly

Telegram uses HTML formatting in DTS templates. Make sure your custom templates use valid HTML tags.
