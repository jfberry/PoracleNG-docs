# `[telegram]`

Telegram bot configuration: token, registration groups, admin permissions and welcome messages.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `enabled` | bool | `true` | Enable the Telegram bot. It only starts when `token` is also set. **5.3+:** `false` also stops all Telegram alerts, and startup warns if a token is set. In 5.2.x it only stopped commands, and alerts were still delivered. `config.example.toml` ships `enabled = false`: set it to `true` (or delete the line) when you add a token. |
| `token` | string | `""` | Bot token from BotFather. An array is accepted, but only the first token is used. |
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
| `disable_auto_greetings` | bool | `false` | Skip the `greeting` DTS message when reconciliation registers a user automatically. |

!!! note
    `check_role` only schedules the check. Actual deletion or disabling depends on `general.role_check_mode`.

## `[[telegram.delegated_admins]]`

Let users manage the tracking of a registered group or forum topic without being full admins. `target` is the chat ID, or `<chatId>:<topicId>` for a single forum topic (a chat ID also covers its topics); `admins` lists Telegram user IDs.

```toml
[[telegram.delegated_admins]]
target = "group_or_channel_id"
admins = ["user_id_1", "user_id_2"]
```

!!! info "PoracleNG 5.3+"
    In 5.2.x this table was only read and logged; it gave nobody any permissions. From 5.3, the listed users can run tracking commands in that chat to change its tracking. With area security on, community admins (`telegram.admins` in an [area security community](area_security.md)) can do the same in any group belonging to their communities, and can run `/userlist` limited to their communities' members.
