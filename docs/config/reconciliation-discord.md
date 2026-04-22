# `[reconciliation.discord]`

Behaviour of the Discord reconciliation pass — what happens when role checks run, or when role changes are observed live on Discord.

Reconciliation is automatic only if `discord.check_role` (or `telegram.check_role`) is enabled. It also runs in response to role-change events.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `update_user_names` | bool | `false` | Follow Discord username changes and update Poracle's record. |
| `remove_invalid_users` | bool | `true` | De-register users who have lost the required role(s) at role-check time. |
| `register_new_users` | bool | `false` | Auto-register users who gain the required role at role-check time. |
| `update_channel_names` | bool | `true` | Keep stored channel names in sync with Discord. |
| `update_channel_notes` | bool | `false` | Update channel notes with the guild name / category. |
| `unregister_missing_channels` | bool | `false` | Remove channels that have been deleted from Discord. |

See also: [`[reconciliation.telegram]`](reconciliation-telegram.md), [`[discord]`](discord.md).
