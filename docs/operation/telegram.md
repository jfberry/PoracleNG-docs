# Telegram Admin Operations

Telegram-specific admin tasks and operations.

## Group Setup

Register a Telegram group to receive alerts by running `/channel add` in the group:

```
/channel add                        # register this group
/channel remove                     # unregister it and delete its tracking
```

Then set up the group's tracking. Commands an admin types in a registered group apply to the group:

```
/area add downtown
/track everything iv100
/raid level5
```

### Forum Topics

In a supergroup with **Topics** enabled, each topic can be a separate alert target. Run `/channel add` inside a topic to register just that topic. Commands typed in that topic then manage the topic's own tracking, and alerts are posted into it.

```
/channel add                        # typed inside the "Hundos" topic → registers only that topic
/track everything iv100
```

Topics are stored with the ID `<groupId>:<topicId>` (type `telegram:topic`). If you register only the parent group, commands typed inside a topic won't find a registered target.

### Channels

Telegram channels can't run bot commands, so register them by ID from a group or private chat (full admins only). The bot must be an admin of the channel so it can post:

```
/channel add name:city-alerts -1001234567890
/channel remove name:city-alerts -1001234567890
```

Manage the channel's tracking with `name:`, for example `/track everything iv100 name:city-alerts`.

## Getting IDs

`/identify` works for anyone, with no registration needed:

| Where | Reply |
|---|---|
| Private chat | Your user ID |
| Group | The group ID and your user ID |
| Forum topic | The group ID, topic ID and your user ID |
| Channel (post `/identify` in the channel) | The channel ID |

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

## Delegated Admins and Tracking Admins

```toml
[telegram]
user_tracking_admins = ["user_id_1"]     # may manage any user's tracking with user:<id>

[[telegram.delegated_admins]]
target = "group_id"
admins = ["user_id_1", "user_id_2"]
```

`user_tracking_admins` lets the listed users add `user:<id>` to tracking commands to manage another user's tracking.

!!! warning
    `[[telegram.delegated_admins]]` is read and logged at startup, but it doesn't currently give the listed users any permissions on Telegram. Until that's fixed, only full `admins` (and, for `/channel add`, community admins from area security) can manage a group's tracking.

Community admins (`telegram.admins` in an [area security](../configuration/areasecurity.md) community) can `/channel add` a group. The group then gets that community's membership and area restriction.

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
