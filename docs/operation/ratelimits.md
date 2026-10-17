# Rate Limits

PoracleNG limits how many alerts each destination can receive so that one over-broad tracking setup can't flood a user, a channel, or the bot's own Discord/Telegram quotas.

## Configuration

```toml
[alert_limits]
timing_period = 240                  # Seconds per limit window
dm_limit = 20                        # Max alerts per user (DM) per window
channel_limit = 40                   # Max alerts per channel / group / webhook per window
dm_summary_limit = 10                # Max summary digests per user per window
channel_summary_limit = 40           # Max summary digests per channel per window
max_limits_before_stop = 10          # Breaches within 24 hours before auto-disable
disable_on_stop = false              # true = admin-disable instead of stop
shame_channel = ""                   # Discord channel to announce banned users
```

See [`[alert_limits]`](../config/alert_limits.md) for the field reference.

## How It Works

1. Each destination has a counter. The window opens with the first alert and lasts `timing_period` seconds (default 240 = 4 minutes).
2. Users (Discord DMs, Telegram private chats) are limited to `dm_limit`. Everything else — Discord channels and threads, Discord webhooks, Telegram groups and channels — uses `channel_limit`.
3. The first alert past the limit sends one notice ("You have reached the limit of N messages over T seconds") and counts as one **breach**. Further alerts in that window are dropped silently.
4. When the window expires the counter resets and alerts flow again.
5. After `max_limits_before_stop` breaches within 24 hours (counted from the first breach), the destination is **banned**: it is disabled and removed from matching.

Rate-limit notices and ban messages bypass the limit, so the destination always learns why its alerts stopped.

### Summary Buckets

[Alert summaries](../userguide/summaries.md) have their own counters: `dm_summary_limit` for users and `channel_summary_limit` for channels. One summary counts once, however many messages it is split into. Summaries and regular alerts don't use up each other's limits, and summary breaches never lead to a ban.

### Who Can Be Banned

Bans apply to **every** destination type: users, channels, threads, Discord webhooks and Telegram groups. When a ban fires:

- The destination is disabled — **stopped** (`enabled = 0`) by default, or **admin-disabled** if `disable_on_stop = true`.
- It gets a farewell message explaining why.
- If `shame_channel` is set **and** the destination is a Discord user, the shame channel gets an @mention. Channels, webhooks and Telegram targets are never posted there.
- If `[discord] admin_channel_id` is set, the admin channel gets a notice for every banned destination type, e.g. ``:no_entry: Rate-limit ban: auto-disabled discord:channel `raids-north` (`123…`)``.
- Tracking state reloads so the destination stops matching straight away.

!!! info "PoracleNG 5.3+: bans fire once"
    In 5.2.x, each later breach in the same 24-hour window (for example while an already-queued backlog drained) triggered the whole ban again: another farewell, shame post and admin notice every `timing_period`. From 5.3 the ban fires only once per 24-hour violation window. Later breaches still count, and `!poracle-admin ratelimit` still shows the destination as banned.

    A side effect: if the destination re-enables itself with `!start` and breaches again within the same 24 hours, it is rate-limited but not banned a second time. Use `!poracle-admin ratelimit reset <id>` to clear its history.

### Stopped vs Disabled

- **Stopped** (default) — the user (or an admin in the channel) re-enables with `!start`. Tracking is kept.
- **Admin-disabled** (`disable_on_stop = true`) — needs an admin to run `!enable <id>`, which stops users from simply restarting a flood.

## Auto-Disable on Delivery Failures

Separately from rate limits, a destination is disabled after **10 consecutive failed sends** (for example a deleted channel, a user who blocked the bot, or a revoked webhook). One successful send resets the count. The destination is stopped (`enabled = 0`), and further sends to it are dropped for 5 minutes. As with bans, the shame channel is notified only for Discord users, and `admin_channel_id` gets a notice for every destination type. The threshold isn't configurable.

## Delivery Lanes

Since 5.2, every destination has its own **delivery lane**: a queue drained independently of the others. A user or channel stuck behind Discord or Telegram rate limits (HTTP 429) backs up only its own lane, not the whole bot.

- **Lane size** is `[tuning] delivery_queue_size` (default 200) **per destination**, not a global total. Memory under a backlog therefore grows with the number of active destinations.
- Lanes are created on first use and removed after 60 seconds idle.
- `concurrent_discord_destinations`, `concurrent_discord_webhooks` and `concurrent_telegram_destinations` still cap how many sends are in flight per platform.

When a lane is full:

| Version | Behaviour |
|---------|-----------|
| 5.2.x | The new alert waits for space. Render workers are shared, so a destination that stays stuck can stall delivery for everyone. |
| 5.3+ | The **oldest** queued alert is dropped to make room for the new one. A lane only fills while its destination is stalled, so the queued alerts are the stalest. Rate-limit notices and ban messages are never dropped. |

Dropped alerts are counted in `poracle_delivery_lane_shed_total`, and a throttled warning is logged:

```
WARN … delivery: lane full for 123456789012345678 — shedding oldest queued messages (total shed: 57)
WARN … [Status] delivery shedding messages: 52 lanes, 260 queued, deepest lane 200/200 (123456789012345678), 1 near-cap, lane-full=57, shed=57
```

See [Monitoring](monitoring.md#delivery-lane-metrics) for the full set of lane metrics.

## Overrides

Override the alert limit for specific destinations. `target` is the destination's ID: a Discord user or channel ID, a Telegram chat ID, or for a Discord webhook its registered ID, which is the **webhook URL** (not its name).

```toml
[[alert_limits.overrides]]
target = "123456789012345678"        # Discord channel ID
limit = 100

[[alert_limits.overrides]]
target = "https://discord.com/api/webhooks/1234/abcd"   # webhook URL
limit = 200
```

Overrides apply to the alert counter only. Summary limits are never overridden.

## Inspecting and Resetting

Admins can see the limiter's live state with `!poracle-admin` (alias `!pa`):

| Command | Purpose |
|---------|---------|
| `!pa ratelimit list` | Every destination currently over a limit or banned, by bucket |
| `!pa ratelimit show <id>` | Counts, limits, window times, 24-hour breach count and ban expiry for one destination |
| `!pa ratelimit reset <id>` | Clear its counters and breach history. This does **not** re-enable it — use `!start` / `!enable` for that |
| `!pa ratelimit userlist` | Currently disabled users (same as `!userlist disabled`) |

The limiter state is held in memory and resets on restart. See [Admin Commands](admincommands.md) for the rest of `!poracle-admin`.

## Recommendations

- Start with the defaults and adjust for your community size.
- Set `[discord] admin_channel_id` so you see bans and delivery-failure disables as they happen.
- Use the shame channel or `!pa ratelimit list` to find users who need to narrow their tracking.
- Consider `disable_on_stop = true` for communities with persistent abusers.
- Keep channel limits above DM limits, since a channel combines many people's tracking.
- Alert on `poracle_delivery_lane_shed_total`. Steady shedding means a destination can't keep up. Usually it is over-tracked; raising `delivery_queue_size` only delays the problem.
