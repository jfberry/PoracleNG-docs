---
title: Commands (Admin) - Discord
nav_order: 3
layout: default
parent: Operation
---

# channel

Allows you to register a telegram channel, or a discord webhook.  See [Telegram](telegram.md)
or [Discord](discord.md) pages for details specific to the endpoint

# autocreate

Discord only, this allows you to create categories and channels automatically.  See [Discord](../configuration/discord.md)

# apply *run commands across multiple channels*

The apply command can be to issue commands against multiple channels, by ID or by name.
Remember `!userlist` will list your users and channels if you need to remember what's going on. You can use % wildcards for matching channel names.

Use the `|` separator to indicate the end of the channel list and start of commands, and between commands. You need the spaces!

`!apply 34455322334 234442223432 | track bulbasaur`

`!apply %events | untrack everything | track fletchling iv95`


# webhook

Discord only, this allows you to create categories and channels automatically.  See [Discord](../configuration/discord.md)

