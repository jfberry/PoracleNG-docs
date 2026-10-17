# `[alert_limits]`

Caps on how many messages a user or channel can receive in a given period. Stops individual users from hogging the system and prevents channel explosions during large events.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `timing_period` | int | `240` | Seconds over which limits are calculated. |
| `dm_limit` | int | `20` | Max messages a user can receive in the period. |
| `channel_limit` | int | `40` | Max messages a channel/group can receive in the period. |
| `dm_summary_limit` | int | `10` | Max [summary](../userguide/summaries.md) messages a user can receive in the period. Separate from `dm_limit`; one summary dispatch counts once, however many messages it is split into. |
| `channel_summary_limit` | int | `40` | Max summary dispatches a channel/group can receive in the period. Separate from `channel_limit`. |
| `max_limits_before_stop` | int | `10` | Times a user can hit the rate limit (within 24h) before being stopped. |
| `disable_on_stop` | bool | `false` | Admin-disable instead of just stopping (requires admin to restart). |
| `shame_channel` | string | `""` | Discord channel **ID** where stopped/disabled Discord users are named. |

## `[[alert_limits.overrides]]`

Override the limit for a specific channel, user, or webhook.

```toml
[[alert_limits.overrides]]
target = "channel_or_user_id"
limit = 100

[[alert_limits.overrides]]
target = "webhook_name"
limit = 200
```

See also: [Rate limits](../operation/ratelimits.md).
