---
title: Telegram
nav_order: 3
layout: default
parent: Configuration
---

# Role checking

Poracle can check that users are still a member of a registration channel. If they
are not then they can have their use of poracle stopped.  Unfortunately Telegram bots
can't get a list of users - and users have to do a *start bot* anyway - so auto registration
is not possible.  There is an option to have poracle attempt a registration on the user
issuing `/start` via direct message (this is what the *start bot* menu option sends) - the
user will still have to qualify by being a member of a registation channel.

# Group tracking

Inside a group issue the command '/channel add' to add the group for notifications; you
can then do all the normal tracking commands within the group.

# Channel tracking

Channels can also have tracking added to them, but configuration for these needs to be done by
admins outside of the channel.  

You first issue the `/identify` command inside the channel to have Poracle report the channel
identity, and then use this in a `/channel add` command. Note that this is a negative number.

`/channel add -xxxxxxx namemychannel` - note that the *name* prefix is a parameter and
an important part of the command.

You then can add trackings by sending DM to Poracle and including the *name* designator.

```
/channel add namehundos -111111111
/area add city namehundos
/track everything iv100 namehundos`
/channel add namepvp -2222222
/area add city namepvp
/track everything ultra1 ultracp2450 namepvp
```

# Stickers

Telegram alerts can send a sticker. Telegram is a bit fussy about these:

* they have to be from a https source,
* need to be in webp format
* if they do not exist it will remember this and not display them for a while rather than checking on every messasge.

# Web page preview
