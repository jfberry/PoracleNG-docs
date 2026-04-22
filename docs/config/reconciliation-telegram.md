# `[reconciliation.telegram]`

Behaviour of the Telegram reconciliation pass. Runs only if `telegram.check_role` is enabled.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `update_user_names` | bool | `false` | Follow Telegram username changes and update Poracle's record. |
| `remove_invalid_users` | bool | `true` | Automatically remove users who lose access. |

See also: [`[reconciliation.discord]`](reconciliation-discord.md), [`[telegram]`](telegram.md).
