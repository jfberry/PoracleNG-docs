---
title: Commands (Admin) - Telegram
nav_order: 4
layout: default
parent: Operation
---


# channel

Although Poracle configuration refers to channels for registration, it really means groups.
Telegram also supports channels - the difference is that a channel does not provide
capability for bots to understand who is sending messages to it.

This obviously causes a problem for security as Poracle needs to know whether to act on
a command.

If you use `/identify` in a channel you will get the message:

```
This channel is id: [ -xxxxxxxxxx ] and your id is: unknown - this is a channel 
(and can't be used for bot registration)
```

You can configure poracle to send alarms to a channel, but configuration for these needs to be done by
admins outside of the channel.

You first issue the `/identify` command inside the channel to have Poracle report the channel
identity, and then use this in a `/channel add` command. Note that this is a negative number.

`!channel add -xxxxxxx namemychannel` - note that the *name* prefix is a parameter title and
an important part of the command.

You then can add trackings by sending DM to Poracle and including the name designator.

`!track everything iv100 namemychannel`

