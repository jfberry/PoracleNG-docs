# Rate Limits

PoracleNG includes rate limiting to prevent individual users or channels from overwhelming the messaging system.

## Configuration

```toml
[alert_limits]
timing_period = 240                  # Seconds over which limits are calculated
dm_limit = 20                        # Max messages per user per period
channel_limit = 40                   # Max messages per channel per period
max_limits_before_stop = 10          # Times hitting limit before user is stopped
disable_on_stop = false              # Admin disable (vs just stop)
shame_channel = ""                   # Discord channel to report stopped users
```

## How It Works

1. Messages to each user/channel are counted over a rolling `timing_period` (default 240 seconds / 4 minutes)
2. If a user exceeds `dm_limit` messages in the period, additional messages are suppressed
3. If a channel exceeds `channel_limit`, additional messages are suppressed
4. Each time a user hits the limit, a counter is incremented
5. After hitting the limit `max_limits_before_stop` times within 24 hours, the user is stopped

### Stopped vs Disabled

- **Stopped** — user can restart with `!start`. Their tracking is preserved.
- **Disabled** (`disable_on_stop = true`) — requires an admin to re-enable with `!enable @user`. Prevents users from immediately restarting a flood.

## Shame Channel

Report users who are stopped/disabled to a Discord channel:

```toml
[alert_limits]
shame_channel = "channel-id"
```

## Overrides

Override limits for specific users, channels, or webhooks:

```toml
[[alert_limits.overrides]]
target = "channel_id"
limit = 100

[[alert_limits.overrides]]
target = "webhook_name"
limit = 200

[[alert_limits.overrides]]
target = "user_id"
limit = 50
```

## Recommendations

- Start with defaults and adjust based on your community size
- Monitor the `logs/errors-*.log` for rate limit messages
- Use the shame channel to identify users who need to narrow their tracking
- Consider `disable_on_stop = true` for communities with persistent rate limit abusers
- Channel limits should be higher than DM limits since channels aggregate multiple users' tracking
