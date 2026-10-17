# Discord Configuration

Full Discord configuration reference. For initial bot setup, see [Discord Bot Setup](../installation/discord.md).

## Basic Settings

```toml
[discord]
enabled = true
token = "bot-token"                      # Bot token
guilds = ["guild-id"]                    # Server IDs
channels = ["channel-id"]               # Registration channel IDs
admins = ["user-id"]                     # Admin user IDs
user_role = ["role-id"]                  # Roles that grant automatic access
prefix = "!"                             # Command prefix
```

## Bot Presence

```toml
[discord]
activity = "PoracleNG"                   # Bot activity status text
```

## Separate Command Bot

By default one bot token handles everything. To split command handling from alert delivery, add a second bot as `command_token`:

```toml
[discord]
token = "delivery-bot-token"             # Sends alerts
command_token = "command-bot-token"      # Commands, reconciliation, role management
```

Both bots must be invited to every guild they operate in. The command bot needs the Server Members and Message Content intents and Manage Roles; the delivery bot only needs send permissions. Users see separate DM conversations for alerts and commands. See [Split-bot operation](../config/discord.md#split-bot-operation).

!!! note
    `token` accepts an array for backward compatibility, but only the first token is used. Extra tokens do **not** spread delivery across several bots.

## Operator Notices and Threads

```toml
[discord]
admin_channel_id = ""                    # Channel for operator notices (auto-disables, keep-alive failures, render errors)
thread_keep_alive_interval_hours = 24    # Unarchive Poracle's threads this often; 0 = off, max 168
```

## Registration and Access

```toml
[discord]
channels = ["channel-id-1", "channel-id-2"]  # Channels where !poracle registers users
user_role = ["role-id"]                        # Roles that auto-grant access
disable_auto_greetings = false                 # Don't greet users who get role access
```

When `user_role` is set, users with those roles are automatically registered without needing to use `!poracle`.

!!! note
    In [area security](areasecurity.md) mode, `channels` and `user_role` are configured per-community instead of here.

## Delegated Admins

Grant admin access to specific channels, guilds, or categories:

```toml
[[discord.delegated_admins]]
target = "channel_or_guild_id"
admins = ["user_id_1", "role_id_2"]

[[discord.delegated_admins]]
target = "category_id"
admins = ["user_id_3"]
```

## Webhook Admins

Grant users admin access to specific Discord webhooks:

```toml
[[discord.webhook_admins]]
target = "webhook_name"
admins = ["user_id_1"]
```

## User Tracking Admins

Allow specific users or roles to manage other users' tracking:

```toml
[discord]
user_tracking_admins = ["user_id_1", "role_id_1"]
```

## Role Subscriptions

Allow users to self-assign roles via `!role` commands:

```toml
[[discord.role_subscriptions]]
guild = "745734887746568214"
roles = { alerts = "917438265009774633", raids = "917438323964903442" }
exclusive_roles = [
  { "team-a" = "919255663841017917", "team-b" = "919255700390170636" },
]
```

- `roles` — independent roles users can freely add/remove
- `exclusive_roles` — groups where a user can only hold one role (e.g. team selection)

Users interact with: `!role list`, `!role add <name>`, `!role remove <name>`, `!role membership`

Roles are automatically removed when a user loses their Poracle membership.

## Command Security

Restrict commands to certain users or roles. Discord only.

```toml
[discord.command_security]
raid = ["role_id_1", "user_id_2"]
poracle_admin = ["role_id_1"]
pvp = ["role_id_1"]
```

Valid keys:

- Whole commands: `monster` (`!track`), `raid`, `egg`, `quest`, `gym`, `lure`, `invasion` (covers `!invasion` and `!incident`), `nest`, `maxbattle`, `poracle_admin` (`!poracle-admin`)
- Features: `pvp` (PVP filters in `!track`), `specificgym` (tracking a specific gym in `!gym`)

Any other key (`area`, `location`, `profile`, `tracked`, `fort`, ...) is silently ignored. In DMs, the user's roles from all configured `guilds` are combined. See [`[discord.command_security]`](../config/discord.md#discordcommand_security) for details.

!!! warning "Sub-table placement"
    `[discord.command_security]`, `[discord.slash_commands]` and the `[[discord.*]]` arrays must come **after** all plain `[discord]` keys. Any `[discord]` key placed below a sub-table header becomes part of that sub-table and is lost.

## Slash Commands

```toml
[discord.slash_commands]
enabled = true
register_globally = false
guilds = ["guild-id"]                    # Per-guild registration: changes show in seconds
```

See [`[discord.slash_commands]`](../config/discord.md#discordslash_commands) and [Slash Commands](../userguide/slash.md).

## Appearance

```toml
[discord]
iv_colors = ["#9D9D9D", "#FFFFFF", "#1EFF00", "#0070DD", "#A335EE", "#FF8000"]
upload_embed_images = false              # Upload images to Discord CDN instead of linking
```

The `iv_colors` array provides 6 color codes used for IV-based embed coloring (from worst to best).

## Logging and Messages

```toml
[discord]
dm_log_channel_id = ""                   # Channel to log all commands to
dm_log_channel_deletion_time = 0         # Minutes before log cleanup (0 = never)
message_delete_delay = 0                 # Extra delay (ms) for message cleanup
unrecognised_command_message = ""        # Reply to unknown commands in DM
unregistered_user_message = ""           # Reply to unregistered users (empty = shrug)
lost_role_message = ""                   # Message when user loses role access
```

## Role Checking

Periodically verify all users still have required roles:

```toml
[discord]
check_role = false                       # Enable periodic role checking
check_role_interval = 6                  # Hours between checks
```

The action taken when users fail role checks is controlled by `[general] role_check_mode`.

## Reconciliation

```toml
[reconciliation.discord]
update_user_names = false                # Follow Discord username changes
remove_invalid_users = true              # De-register users who lose roles
register_new_users = false               # Auto-register users granted roles
update_channel_names = true              # Keep channel names in sync
update_channel_notes = false             # Update channel notes with guild/category
unregister_missing_channels = false      # Remove channels deleted from Discord
```
