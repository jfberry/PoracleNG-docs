# `[discord]`

Discord bot configuration: tokens, registration channels, admin permissions, command security, and per-server role subscriptions.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `enabled` | bool | `true` | Enable the Discord controller. |
| `activity` | string | `"PoracleNG"` | Bot activity text. |
| `disable_auto_greetings` | bool | `false` | Skip greeting users who gain role-based access. |
| `upload_embed_images` | bool | `false` | Re-upload embed images directly to the Discord CDN. |
| `check_role` | bool | `false` | Periodically verify role membership for all users. |
| `check_role_interval` | int | `6` | Hours between role checks. |
| `token` | array | `[""]` | Array of Discord tokens. The first token acts as the command controller; additional tokens are workers. |
| `guilds` | array | `[""]` | Guild (server) IDs Poracle operates in. |
| `channels` | array | `[""]` | Channel IDs that can be used for `!poracle` registration. (Not used in area-security mode.) |
| `user_role` | array | `[""]` | Role IDs that automatically grant access without registration. (Not used in area-security mode.) |
| `admins` | array | `[""]` | User IDs of bot admins. |
| `user_tracking_admins` | array | `[]` | User/role IDs that may manage other users' tracking. |
| `prefix` | string | `"!"` | Command prefix. |
| `iv_colors` | array(string) | 6 hex codes | Six colour codes for the IV ranking palette. |
| `dm_log_channel_id` | string | `""` | Channel ID to mirror all Poracle commands into. |
| `dm_log_channel_deletion_time` | int | `0` | Minutes after which DM log entries are deleted (`0` = never). |
| `message_delete_delay` | int | `0` | Extra time (ms) added before a `clean` is performed. |
| `unrecognised_command_message` | string | `""` | Reply to unrecognised DM commands. |
| `unregistered_user_message` | string | `""` | Reply to unregistered users (empty = shrug). |
| `lost_role_message` | string | `""` | Message sent when a user loses role-based access. |

!!! note "Role checks require `general.role_check_mode`"
    Setting `check_role = true` only schedules the check. Actual deletion or disabling depends on `general.role_check_mode`.

## `[[discord.delegated_admins]]`

Grant users admin authority over a specific channel, guild, or category.

```toml
[[discord.delegated_admins]]
target = "channel_or_guild_id"
admins = ["user_id_1", "role_id_2"]
```

## `[[discord.webhook_admins]]`

Grant users admin over named webhooks.

```toml
[[discord.webhook_admins]]
target = "webhook_name"
admins = ["user_id_1"]
```

## `[[discord.role_subscriptions]]`

Self-service role assignment via `!role list`, `!role add <name>`, `!role remove <name>`, `!role membership`. Roles are automatically removed when a user loses Poracle membership.

- `roles` — independent roles that users can freely add or remove.
- `exclusive_roles` — groups where the user can hold at most one role per group (e.g. team selection).

```toml
[[discord.role_subscriptions]]
guild = "745734887746568214"
roles = { alerts = "917438265009774633", raids = "917438323964903442" }
exclusive_roles = [
  { "team-a" = "919255663841017917", "team-b" = "919255700390170636" },
  { "color-red" = "919255733592289341", "color-blue" = "919255750948323340" },
]
```

## `[discord.command_security]`

Restrict commands to specific user or role IDs. Valid commands: `monster`, `pvp`, `gym`, `invasion`, `lure`, `nest`, `quest`, `egg`, `raid`, `fort`, `area`, `location`, `profile`, `tracked`, `script`, `start`, `language`.

```toml
[discord.command_security]
monster = ["userid", "roleid"]
pvp = ["roleid"]
```
