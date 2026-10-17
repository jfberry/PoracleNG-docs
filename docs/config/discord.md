# `[discord]`

Discord bot configuration: tokens, registration channels, admin permissions, command security, slash commands and per-server role subscriptions.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `enabled` | bool | `true` | Start the Discord gateway bot (commands, reconciliation, role management). Set `false` to keep it off even when a token is configured. **5.3+:** `false` also stops all Discord alerts, including Discord webhooks, and startup warns if a token is set. In 5.2.x it only stopped the gateway, and alerts were still delivered. |
| `token` | string or array | (none) | Bot token. Used for all outbound delivery (DMs, channel and thread sends, edits, clean deletes). When `command_token` is empty it also drives the gateway side. Only the **first** token is used; extra entries in an array are ignored. |
| `command_token` | string | `""` | Optional second bot token for the gateway side (commands, reconciliation, role management). See [Split-bot operation](#split-bot-operation). |
| `activity` | string | `"PoracleNG"` | Bot activity text. |
| `prefix` | string | `"!"` | Command prefix. |
| `guilds` | array | `[]` | Guild (server) IDs Poracle operates in. |
| `channels` | array | `[]` | Channel IDs that can be used for `!poracle` registration. (Not used in area-security mode.) |
| `user_role` | array | `[]` | Role IDs that automatically grant access without registration. (Not used in area-security mode.) |
| `admins` | array | `[]` | User IDs of bot admins. |
| `user_tracking_admins` | array | `[]` | User/role IDs that may manage other users' tracking. |
| `admin_channel_id` | string | `""` | Channel ID for operator notices: users/channels auto-disabled (delivery failures, rate-limit bans), thread keep-alive failures, DTS render errors, state-reload failures. Empty = off. |
| `thread_keep_alive_interval_hours` | int | `0` | How often (hours) Poracle unarchives the Discord threads it delivers to, so they stay usable. `0` = off. `config.example.toml` sets the recommended `24`, but if you leave the key out the sweep is **off**. Values above `168` (7 days, Discord's maximum archive time) are clamped to `168`. |
| `disable_auto_greetings` | bool | `false` | Skip greeting users who gain role-based access. |
| `upload_embed_images` | bool | `false` | Re-upload embed images directly to the Discord CDN. |
| `check_role` | bool | `false` | Periodically verify role membership for all users. |
| `check_role_interval` | int | `6` | Hours between role checks. |
| `lost_role_message` | string | `""` | Message sent when a user loses role-based access. |
| `iv_colors` | array(string) | 6 hex codes | Exactly six colour codes for the IV ranking palette. |
| `dm_log_channel_id` | string | `""` | Channel ID to mirror all Poracle DM commands into. |
| `dm_log_channel_deletion_time` | int | `0` | Minutes after which DM log entries are deleted (`0` = never). |
| `message_delete_delay` | int | `0` | Extra time (ms) added before a channel `clean` delete. |
| `unrecognised_command_message` | string | `""` | Reply to unrecognised DM commands (empty = built-in translated message). |
| `unregistered_user_message` | string | `""` | Reply to unregistered users (empty = shrug). |

!!! note "Role checks require `general.role_check_mode`"
    Setting `check_role = true` only schedules the check. Actual deletion or disabling depends on [`general.role_check_mode`](general.md#role_check_mode).

!!! note "Dead keys"
    `worker_status` and `worker_activity` are not read by PoracleNG and can be removed. They appear in older configs and in the 5.2.x `config.example.toml`; the 5.3 example no longer has them.

!!! warning "Put `[discord.*]` sub-tables last"
    In TOML, a table header such as `[discord.command_security]`, `[discord.slash_commands]` or `[[discord.role_subscriptions]]` captures **every key below it** until the next header. If you put one of these above plain `[discord]` keys (`prefix`, `iv_colors`, `admin_channel_id`, ...), those keys silently become part of the sub-table and your `[discord]` settings are lost. Keep all sub-tables at the end of the `[discord]` section.

## Split-bot operation

By default one bot does everything. Setting `command_token` splits Poracle into two bots:

- The `token` bot handles outbound alert delivery.
- The `command_token` bot handles the gateway: commands, reconciliation and role management.

Both bots must be invited to every guild they work in, each with its own permissions. The command bot needs the **Server Members** and **Message Content** intents plus **Manage Roles** for reconciliation; the delivery bot only needs send permissions. Users see two separate DM conversations, one for alerts and one for commands.

If `command_token` is set but that bot fails to connect, PoracleNG exits instead of falling back to single-bot mode.

```toml
[discord]
token = "delivery-bot-token"
command_token = "command-bot-token"
```

## `[[discord.delegated_admins]]`

Let users manage the tracking of registered channels without being full admins. `target` is a channel, guild or category ID; `admins` lists user and role IDs.

```toml
[[discord.delegated_admins]]
target = "channel_guild_or_category_id"
admins = ["user_id_1", "role_id_2"]
```

A command typed in a registered channel is checked against the channel's ID, its guild, and its ancestors: a text channel's category, or a thread's parent channel and that channel's category.

!!! info "PoracleNG 5.3+"
    Category targets (and thread parents) work from 5.3. In 5.2.x only channel and guild IDs were checked, so a category entry granted nothing. Slash commands always act on the person running them, so delegation applies to `!` commands only. See [Discord admin operations](../operation/discord.md#delegated-admins).

## `[[discord.webhook_admins]]`

Grant users admin over named webhooks.

```toml
[[discord.webhook_admins]]
target = "webhook_name"
admins = ["user_id_1"]
```

## `[[discord.role_subscriptions]]`

Self-service role assignment via `!role list`, `!role add <name>`, `!role remove <name>`, `!role membership`. Roles are automatically removed when a user loses Poracle membership.

- `roles`: independent roles that users can freely add or remove.
- `exclusive_roles`: groups where the user can hold at most one role per group (e.g. team selection).

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

Restrict commands, or features within a command, to specific user or role IDs. A key with a non-empty list restricts it to those IDs; everyone else gets a 🙅 reaction. A missing key or empty list means unrestricted. Discord only (Telegram has no role-based command security).

**Whole-command keys:**

| Key | Restricts |
|---|---|
| `monster` | `!track` |
| `raid` | `!raid` |
| `egg` | `!egg` |
| `quest` | `!quest` |
| `gym` | `!gym` |
| `lure` | `!lure` |
| `invasion` | `!invasion` and `!incident` |
| `nest` | `!nest` |
| `maxbattle` | `!maxbattle` |
| `poracle_admin` | `!poracle-admin` |

**Feature keys** (restrict a feature inside a command):

| Key | Restricts |
|---|---|
| `pvp` | Using PVP filters (`great5`, `ultra1`, ...) in `!track` |
| `specificgym` | Tracking a specific gym in `!gym` |

Any other key (`area`, `location`, `profile`, `tracked`, `fort`, `start`, ...) cannot be gated and is **silently ignored**.

```toml
[discord.command_security]
raid = ["role_id_1", "user_id_2"]
poracle_admin = ["role_id_1"]
pvp = ["role_id_1"]
```

Role matching: in a server channel, the user's roles in that server are used. In DMs, Discord doesn't say which server the user belongs to, so Poracle combines the user's roles from **all** guilds listed in `guilds`.

!!! note
    Bot admins (`[discord] admins`) always bypass `command_security`. From 5.3 this applies to `!` text commands, slash commands and `POST /api/command` alike. In 5.2.x only slash commands let admins through; for text commands, include admin IDs in the list.

## `[discord.slash_commands]`

Optional Discord slash commands, registered alongside the `!` text commands. Both keep working. See [Slash commands](../userguide/slash.md) for the user side.

| Field | Type | Default | Description |
|---|---|---|---|
| `enabled` | bool | `false` | Master switch. When `false`, no slash commands are registered or handled. |
| `register_globally` | bool | `false` | Register one global command set (every guild the bot is in, plus DMs). Global changes can take up to an hour to show in Discord clients. |
| `guilds` | array | `[]` | Guild IDs to register commands in. Only used when `register_globally = false`. Per-guild changes show within seconds. |
| `enable` | array | `[]` | Optional allow-list of canonical English command names (e.g. `track`, `raid`). Empty = every slash command this build supports. |

If `enabled = true` but `register_globally = false` and `guilds` is empty, nothing is registered and a warning is logged.

```toml
[discord.slash_commands]
enabled = true
register_globally = false
guilds = ["745734887746568214"]
# enable = ["track", "tracked", "raid", "untrack", "area", "profile", "location", "start", "stop"]
```

Switching between global and per-guild registration leaves the old registrations behind, so users see duplicates. Start PoracleNG once with one of these flags to clean up:

| Flag | Effect |
|---|---|
| `-clear-global-slash-commands` | Remove all globally registered commands at startup (after switching `register_globally` from `true` to `false`). |
| `-clear-guild-slash-commands` | Remove guild-scoped commands from the guilds in `[discord.slash_commands] guilds` at startup (after switching to `register_globally = true`). |

See also: [Discord configuration guide](../configuration/discord.md), [`[reconciliation.discord]`](reconciliation-discord.md).
