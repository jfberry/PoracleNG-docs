# Discord Admin Operations

Discord-specific admin tasks and operations.

## Channel Setup

Register Discord channels to receive alerts:

```
!channel add                        # Register current channel
!channel add #alerts-pokemon        # Register a named channel
!channel remove                     # Unregister current channel
```

After adding a channel, configure its areas and tracking just like a user:

```
!area add downtown #alerts-pokemon
!track everything iv100 #alerts-pokemon
!raid level5 #alerts-pokemon
```

## Webhook Integration

Discord webhooks can also be used as alert destinations. Configure webhooks through the Discord channel settings (Integrations → Webhooks), then register them with Poracle.

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

Allow users to self-manage roles:

```
!role list                          # Available roles
!role add alerts                    # Add a role
!role remove alerts                 # Remove a role
!role membership                    # Show current roles
```

## Delegated Admins

Grant admin access to specific users for specific channels or guilds:

```toml
[[discord.delegated_admins]]
target = "channel_id"
admins = ["user_id_1", "role_id_2"]
```

Delegated admins can manage tracking for their assigned channels/guilds but don't have global admin access.

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
