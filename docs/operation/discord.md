# Discord Admin Operations

Discord-specific admin tasks and operations.

## Channel Setup

Register a Discord channel to receive alerts by running `!channel add` **in that channel**. It registers the channel you type it in; you can't register a channel by mentioning it.

```
!channel add                        # register this channel (or thread)
!channel add area:downtown          # …and set an initial area
!channel remove                     # unregister this channel and delete its tracking
```

Then configure the channel's areas and tracking. Commands typed in a registered channel by an admin or delegated admin apply to that channel:

```
!area add downtown
!track everything iv100
!raid level5
```

To manage a channel's tracking from somewhere else, use `name:<channel-name>` (for example `!tracked name:alerts-pokemon`). See [Tracking on Behalf of Others](admincommands.md#tracking-on-behalf-of-others).

## Webhook Integration

Discord webhooks can be alert targets too. Webhook delivery doesn't need the bot to have access to the channel. Use `!webhook add` to create (or reuse) a channel's `Poracle` webhook and register it in one step:

```
!webhook add                        # register this channel's webhook, named after the channel
!webhook add name:city-hundos       # …with a different name
!webhook list                       # DM you the existing webhooks and URLs for this channel
!webhook create                     # just create a webhook and DM you the URL
!webhook remove name:city-hundos    # unregister it
```

If you already have a webhook URL, register it from anywhere, including a DM:

```
!channel add name:city-hundos https://discord.com/api/webhooks/…
```

Manage a webhook's tracking with `name:`, for example `!track everything iv100 name:city-hundos`. See [Admin Commands](admincommands.md#registering-channels-groups-and-webhooks).

## Multiple Bots

If running multiple bot tokens, the first bot handles commands while additional bots distribute message delivery:

```toml
[discord]
token = ["command-bot", "worker-1", "worker-2"]
```

Worker bots need to be invited to the same server with the same permissions.

## Role Management

### Checking Roles

Enable periodic role checking to manage users who lose their access roles:

```toml
[discord]
check_role = true
check_role_interval = 6              # Check every 6 hours
```

What happens when a user loses their role depends on `[general] role_check_mode`:

- `ignore` — log only, no action
- `delete` — remove user and all tracking
- `disable-user` — disable until role is restored

### Role Subscriptions

Let users add and remove Discord roles themselves:

```toml
[[discord.role_subscriptions]]
guild = "guild_id"
roles = { alerts = "role_id_1", raids = "role_id_2" }
exclusive_roles = [
  { "team-a" = "role_id_3", "team-b" = "role_id_4" },
]
```

Roles in an `exclusive_roles` group are mutually exclusive: adding one removes the others in that group. Users run `!role` in a DM with the bot:

```
!role list                          # available roles (☑️ = you have it)
!role membership                    # only the roles you have
!role add alerts                    # add a role
!role remove alerts                 # remove a role
```

Admins can also use `!role` in channels, and add `user:<id>` to change another user's roles. If no role subscriptions are configured, `!role` replies 🙅.

## Delegated Admins

Grant tracking management for specific channels or whole guilds without full admin access:

```toml
[[discord.delegated_admins]]
target = "channel_or_guild_id"
admins = ["user_id_1", "role_id_2"]

[[discord.webhook_admins]]
target = "webhook_name"
admins = ["user_id_1"]
```

and, as a plain `[discord]` key:

```toml
user_tracking_admins = ["user_id_1", "role_id_1"]
```

| Setting | Grants |
|---|---|
| `delegated_admins` | Running tracking commands inside a registered channel in that channel or guild changes **the channel's** tracking. |
| `webhook_admins` | Managing that webhook's tracking with `name:<webhook_name>`. Only user IDs are checked here, not role IDs. |
| `user_tracking_admins` | Managing any registered user's tracking with `user:<id>`. |

Delegated admins can't run admin commands such as `!channel`, `!userlist` or `!pa`.

!!! warning
    The config comments say a `delegated_admins` target can be a category ID, but only channel and guild IDs are currently checked. Use the guild ID, or list each channel.

!!! tip
    `[[discord.…]]` tables must come **after** all plain `[discord]` keys in `config.toml`, otherwise later keys get pulled into the table.

## Command Logging

Log all user commands to a specific channel:

```toml
[discord]
dm_log_channel_id = "log-channel-id"
dm_log_channel_deletion_time = 0     # 0 = keep forever, or minutes to auto-delete
```

## Reconciliation

Automatic sync operations run periodically when `check_role = true`:

```toml
[reconciliation.discord]
update_user_names = false            # Follow username changes
remove_invalid_users = true          # De-register users who lose roles
register_new_users = false           # Auto-register users who gain roles
update_channel_names = true          # Keep channel names in sync
unregister_missing_channels = false  # Remove deleted channels
```
