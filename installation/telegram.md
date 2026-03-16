---
title: Telegram
nav_order: 6
layout: default
parent: Installation
---
# Create a telegram bot and record the token

# Configure basic telegram settings

| Option        | Value         | Description |
| ------------- |---------------| ------------|
| enabled | true | Enables Telegram support |
| checkRole | false | Role check best left for later - you don't want to deregister your test users! |
| token | \[ "token" \] | Your discord bot |
| channels | Array of groups IDs where users can register in. Leave blank for first run - see below |
| admins | \[ "yourid" \] | Array of IDs of administrators - leave blank for first run - see below |


# Find your identity

Start poracle.

Send a direct message to the bot with the text `/identify` - this should respond with something like 
`This is a private message and your id is: [ xxxxxxxxxxxxx ]`.  This number is your 
telegram user id, and should go into the `admins` section of the configuration.

# Create a group for your registration group

Create a group and invite your Poracle bot to the group.  Now you can issue the `/identify` command
to discover the ID number of the group.  Note that this will be a negative number.

You add this number to the configuration as the registration channel.  Members of this
group will be able to send `/poracle` to register.  Note that telegram also requires
them to select the bot profile and select the 'start bot' option.  This link reportedly
will work https://t.me/name-of-your-bot?start to get them straight to the screen.
Poracle provides an option to reply in-channel with a custom message on registration to 
help users with this part of a process

# Group based tracking

Admins can register any group that the Poracle bot is a member of using the `/channel add`
command. After that tracking commands can be issued and results will be sent to the group.

