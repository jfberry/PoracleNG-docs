# `[telegram]`

Telegram bot configuration: token, registration groups, admin permissions and welcome messages.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `enabled` | bool | `false` | Enable the Telegram controller. |
| `token` | string | `""` | Bot token from BotFather. |
| `admins` | array | `[""]` | Admin user IDs. |
| `user_tracking_admins` | array | `[]` | User IDs that may manage other users' tracking. |
| `channels` | array | `[""]` | Group IDs allowed for registration. IDs that don't start with `-100` may not be supergroups yet. Use `/identify` to discover group/user IDs. (Not used in area-security mode.) |
| `group_welcome_text` | string | (default greeting) | Sent in the channel when a user registers. Reminds users to start the bot in DM. |
| `bot_welcome_text` | string | `"You are now registered with Poracle"` | DM sent on successful registration. |
| `bot_goodbye_message` | string | `""` | Sent when a user loses access via reconciliation. |
| `unregistered_user_message` | string | `""` | DM reply to unregistered users. |
| `unrecognised_command_message` | string | `""` | DM reply to unrecognised commands. |
| `check_role` | bool | `false` | Periodically verify users still belong to a registration group. |
| `check_role_interval` | int | `6` | Hours between checks. |
| `register_on_start` | bool | `false` | Attempt auto-registration when a user sends `/start`. |

!!! note
    `check_role` only schedules the check. Actual deletion or disabling depends on `general.role_check_mode`.

## `[[telegram.delegated_admins]]`

Grant users admin authority over a specific group or channel.

```toml
[[telegram.delegated_admins]]
target = "group_or_channel_id"
admins = ["user_id_1", "user_id_2"]
```
