# Telegram Admin Operations

Telegram-specific admin tasks and operations.

## Group Setup

Register Telegram groups to receive alerts:

```
/channel add                        # Register current group
/channel remove                     # Unregister current group
```

Then configure tracking for the group:

```
/area add downtown
/track everything iv100
/raid level5
```

## Getting IDs

Use `/identify` in a group or private chat to get the group ID, user IDs, and other information needed for configuration.

!!! tip
    Group IDs start with `-100`. If your group ID doesn't start with this prefix, it's not a supergroup yet. Enable any admin setting in group management to convert it.

## Membership Checking

Enable periodic membership verification:

```toml
[telegram]
check_role = true
check_role_interval = 6
```

Users who are no longer members of the registration group are handled according to `[general] role_check_mode`.

## Delegated Admins

Grant admin access for specific groups:

```toml
[[telegram.delegated_admins]]
target = "group_id"
admins = ["user_id_1", "user_id_2"]
```

## Messages

Customize bot messages:

```toml
[telegram]
group_welcome_text = "Welcome {user}, remember to click on me and 'start bot' to be able to receive messages"
bot_welcome_text = "You are now registered with Poracle"
bot_goodbye_message = ""
unregistered_user_message = ""
unrecognised_command_message = ""
```

## Reconciliation

```toml
[reconciliation.telegram]
update_user_names = false
remove_invalid_users = true
```
