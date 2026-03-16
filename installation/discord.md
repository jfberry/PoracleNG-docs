---
title: Discord
nav_order: 5
layout: default
parent: Installation
---

# Create a discord bot and record the token

1. Create your discord bot.  See [here](../discordbot.md) for details on how to create your 
   bot & get its token
2. Invite your bot to your server (guild)
3. You can get your guild id and user role by right clicking and selecting 'copy id'

Set your local.json with the following options:


| Option        | Value         | Description |
| ------------- |---------------| ------------|
| enabled | true | Enables Discord support |
| checkRole | false | Role check best left for later - you don't want to deregister your test users! |
| token | \[ "token" \] | Your discord bot |
| guilds | \[ "id" \] | Array of Discord servers Poracle will be a member of |
| channels | Array of channel IDs where users can register in |
| userRole |  | Array of role IDs where users are automatically registered with |
| admins | \[ "yourid" \] | Array of IDs of administrators |
| prefix | "!" | Symbol to use as command prefix |

# Start Poracle

Once you start poracle for this first time you should see Poracle become active, and
then you can test your registration:

# Register with poracle

Go to your registration channel and issue the !poracle command, you should get
a direct message from Poracle welcoming you

# Try some basic tracking

* `!location lat, lon` - set your location to the middle of your area
* `!track everything iv75 d1000` - track all iv95 pokemon within a 1km area

When you are done:
* `!untrack everything'


