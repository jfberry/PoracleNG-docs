# Telegram Configuration

Full Telegram configuration reference. For initial bot setup, see [Telegram Bot Setup](../installation/telegram.md).

## Basic Settings

```toml
[telegram]
enabled = true                           # false stops Telegram commands and (5.3+) all Telegram alerts
token = ""                               # Bot token from BotFather
admins = ["user-id"]                     # Admin user IDs
channels = ["group-id"]                  # Registration group IDs
```

## Registration

```toml
[telegram]
register_on_start = false                # Auto-register users on /start
group_welcome_text = "Welcome {user}, remember to click on me and 'start bot' to be able to receive messages"
bot_welcome_text = "You are now registered with Poracle"
bot_goodbye_message = ""                 # Message when user loses access
```

The `{user}` placeholder in `group_welcome_text` is replaced with the user's name.

## Delegated Admins

Let users manage tracking for specific groups or forum topics:

```toml
[[telegram.delegated_admins]]
target = "group_or_channel_id"           # chat ID, or <chatId>:<topicId> for one forum topic
admins = ["user_id_1", "user_id_2"]
```

!!! info "PoracleNG 5.3+"
    Delegated admins work from 5.3: the listed users can run tracking commands in that chat to change its tracking. In 5.2.x the setting was only read and logged, and gave nobody any permissions. See [Telegram Admin Operations](../operation/telegram.md#delegated-admins-and-tracking-admins).

## User Tracking Admins

Allow specific users to manage other users' tracking:

```toml
[telegram]
user_tracking_admins = ["user_id_1"]
```

These users can add `user:<id>` to tracking commands (for example `/track pikachu iv100 user:123456789`) to act on that registered user. See [Tracking on Behalf of Others](../operation/admincommands.md#tracking-on-behalf-of-others).

## Messages

```toml
[telegram]
unrecognised_command_message = ""        # Reply to unknown commands
unregistered_user_message = ""           # Reply to unregistered users (empty = shrug)
```

## Role Checking

Periodically verify users are still members of registration groups:

```toml
[telegram]
check_role = false                       # Enable periodic membership checking
check_role_interval = 6                  # Hours between checks
```

## Reconciliation

```toml
[reconciliation.telegram]
update_user_names = false                # Follow Telegram username changes
remove_invalid_users = true              # Remove users who lose group membership
```
