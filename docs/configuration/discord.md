# Discord Configuration

Full Discord configuration reference. For initial bot setup, see [Discord Bot Setup](../installation/discord.md).

## Basic Settings

```toml
[discord]
enabled = true
token = ["bot-token"]                    # Array of bot tokens
guilds = ["guild-id"]                    # Server IDs
channels = ["channel-id"]               # Registration channel IDs
admins = ["user-id"]                     # Admin user IDs
user_role = ["role-id"]                  # Roles that grant automatic access
prefix = "!"                             # Command prefix
```

## Bot Presence

```toml
[discord]
activity = "PoracleJS"                   # Bot activity status text
worker_status = "invisible"              # available, dnd, idle, invisible
worker_activity = "PoracleJS Helper"     # Worker bot activity text
```

## Multiple Bots

PoracleNG supports multiple Discord bot tokens. The first token is the command controller; additional tokens are message delivery workers:

```toml
[discord]
token = ["main-bot-token", "worker-1-token", "worker-2-token"]
```

This distributes message delivery across multiple bots to avoid rate limits on busy servers.

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

Grant admin access over specific channels, guilds, or categories:

```toml
[[discord.delegated_admins]]
target = "channel_or_guild_id"
admins = ["user_id_1", "role_id_2"]

[[discord.delegated_admins]]
target = "category_id"
admins = ["user_id_3"]
```

## Webhook Admins

Grant users admin access over specific Discord webhooks:

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

Restrict specific commands to certain users or roles:

```toml
[discord.command_security]
monster = ["userid", "roleid"]
pvp = ["roleid"]
```

Valid commands: `monster`, `pvp`, `gym`, `invasion`, `lure`, `nest`, `quest`, `egg`, `raid`, `fort`, `area`, `location`, `profile`, `tracked`, `script`, `start`, `language`

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
