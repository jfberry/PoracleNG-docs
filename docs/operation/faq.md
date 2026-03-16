# FAQ & Troubleshooting

## General

### How do I update PoracleNG?

```sh
git pull
make build
./start.sh
```

Database migrations are applied automatically on startup.

### Can I run PoracleNG and PoracleJS side by side?

Not on the same database simultaneously. However, you can switch between them as they share the same database schema. See the [Migration Guide](../migration/guide.md#rollback).

### Does PoracleNG support Docker?

Docker support is planned but not yet available. Use the [Manual Installation](../installation/manual.md) method.

## Startup Issues

### Processor won't start

- Verify Go is installed: `go version` (needs 1.21+)
- Check `logs/processor.log` for error details
- Verify database credentials in `config/config.toml`
- Ensure MySQL/MariaDB is running and accessible

### Alerter won't start

- Verify Node.js is installed: `node --version` (needs 18+)
- Run `cd alerter && npm install` to reinstall dependencies
- Check `logs/general-*.log` for errors
- Ensure the processor is running (alerter needs it for reload notifications)

### "Port already in use" error

Another process is using port 3030 or 3031. Either stop the other process or change the ports in `config.toml`:

```toml
[processor]
port = 3030

[alerter]
port = 3031
```

## Alert Issues

### No alerts being sent

1. **Check webhooks are arriving** — look at `logs/processor.log` for incoming webhook messages
2. **Verify scanner config** — webhooks must be sent to the **processor** on port 3030
3. **Check user has tracking** — `!tracked` should show active subscriptions
4. **Check user has areas/location** — tracking requires at least one area or a location with distance
5. **Check bot permissions** — the Discord bot needs Send Messages permission in DM or channels
6. **Check rate limits** — user may be stopped. Admin can `!enable @user`

### Alerts are delayed

- Check `[tuning]` settings, especially `concurrent_*` values
- Monitor queue depth via Prometheus metrics at `/metrics`
- Check if rate limiting is throttling delivery
- Review database performance

### Bot doesn't respond to commands

- Ensure **Message Content Intent** is enabled in Discord Developer Portal
- Check the bot is online (not showing as offline in Discord)
- Verify the command prefix matches config (`!` by default)
- Check `logs/commands-*.log` for command processing errors

### Test command works but real alerts don't

- The `!poracle-test` command uses sample data, not real webhooks
- Verify your scanner is sending webhooks to port 3030
- Check `logs/processor.log` for webhook processing activity

## Configuration Issues

### Config changes not taking effect

- Restart both the processor and alerter after config changes
- Verify syntax in `config.toml` — TOML is strict about quotes and types
- Check for typos in setting names (all `snake_case`)

### Geofences not loading

- Check file paths in `[geofence] paths` are correct relative to the project root
- Verify JSON syntax in geofence files
- Check `logs/processor.log` for geofence loading messages

## Database Issues

### Migration errors on startup

The processor uses golang-migrate for database schema management. If you see migration errors:

1. Check that the database user has ALTER TABLE permissions
2. Look at the specific error in `logs/processor.log`
3. The database may need manual intervention if a previous migration was interrupted

### High database load

- Review `[tuning] max_database_connections`
- Check if `[tuning] reload_interval_secs` is too frequent
- Ensure database indexes are intact

## Discord-Specific

### Bot shows as offline

- Verify the bot token is correct
- Check that the bot application hasn't been disabled
- Look for authentication errors in `logs/general-*.log`

### Missing permissions

The bot needs: Send Messages, Manage Messages, Embed Links, Attach Files, Read Message History, Use External Emojis, Add Reactions. Add Manage Roles if using role subscriptions.

### Rate limited by Discord

Discord enforces global rate limits. If you're hitting them:

- Add more bot tokens as workers
- Reduce `concurrent_discord_destinations` in tuning
- Ensure users aren't tracking too broadly

## Telegram-Specific

### Bot can't send messages to users

Users must click "Start" on the bot in a private chat before the bot can message them. This is a Telegram requirement.

### Messages not formatted correctly

Telegram uses HTML formatting in DTS templates. Make sure your custom templates use valid HTML tags.
