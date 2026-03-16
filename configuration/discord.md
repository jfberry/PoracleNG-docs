---
title: Discord
nav_order: 2
layout: default
parent: Configuration
---

Discord support uses a bot (see [Bot](../discordbot.md) on how to configure) to send alerts
to users. It can also use this bot to message channels, or you can use a webhook.

# User registration

Users can be registered with poracle using a 'call command' - by default `!poracle` or
automatically when assigned a specific role.

# Webhook or Bot for channels?

Adding a channel to poracle through a bot is easy.  Simply go into the channel and issue 
`!channel add` - you will then be able to send all the normal tracking commands.
All messages will be sent from the Poracle Bot that you configured.

Webhooks are slightly more complicated to manage, but have the advantage that you can
override the name of the sender and avatar picture for each message.

Existing webhooks can be registered as a channel using:
`!channel add http://discord.com/webhookurl namemychannel` - note that the *name* prefix is a parameter title and
an important part of the command.
You then can add trackings by sending DM to Poracle and including the name designator.

`!track everything iv100 namemychannel`

If you don't have a webhook created already, poracle can add one using the `!webhook` command
- eg `!webhook create`.  There is a combination command `!webhook add`which adds a hook and
registers the channel for receiving webhooks. By default the name with be the name of the
channel but - you can specify an alternative name using `name` as with channel add above.
  
# Overriding bot name & image for webhooks

You can specify a `username` and `avatar_url` to override the bot name and image; but this only
works for webhooks

```json
  "platform": "discord",
  "template": {
    "username": "{{name}}",
    "avatar_url": "{{{imgUrl}}}",
    "embed": {

```

# Command Security

The command security section allows you to specify who can access certain functions

```json
    // commandSecurity - list commands and users/roles that can execute these commands. If not specified no
    // security is enforced for a command
    // Valid permissions that be restricted: monster, pvp, gym, invasion, lure, nest, ...
    // eg:
    //   "commandSecurity": {
    //      "monster": [ "userid", "roleid" ],
    //      "pvp": [ "roleid" ]
    //   }
      "commandSecurity": {

      },
```

# Channel auto creation

The `!autocreate` command can create a category and channels for your new area automatically.  Create a file channelTemplate.json in your config directory (example in defaults) with a definition.  Configurable fields can be use starting with {0} which can be used for whatever you want - area, language, etc.
Poracle can register the channels (or leave as text/voice only), or create a webhook and set that up.  It can also create or search and use roles by name to make your category and/or channels private, or public with special role priveldges.
Poracle needs to know the guild to work on; so if you execute it in a channel it can work this out or specify with the guild.

Example commands:

* `!autocreate newSection "new york"` - would execute the below with {0} being newyork. Use this in the guild you are adding to.
* `!autocreate guild1234567890 newSection "new york" manhattan` - would execute the below on guild id 1234567890 with {0} being new york and {1} being manhattan. Use this in a DM or another guilds channel.

Notes:
- If you have multiple roles with the same name, the lowest one will be used. The best use case for this is unique area/role names.
- Excluding controlType will create a channel not registered to Poracle with the settings specified
- Any or all of the permissions listed can be set true, false or removed entirely. Think of it as ❌`/`✔️ in the permissions window.
- All currently available permissions are shown in the first example. Soundboard will be added on next Discord.js update (v9 API req)
- @everyone set as shown will create the category/channel as 'private' with the lock symbol. You can manipulate it like any other role as well
- I don't think registering Voice channels or creating webhooks for them is a thing (or a good idea) so don't use any `"controlType"` with `"channelType": voice`
- Channels with no roles array will inherit permissions from the category. Also, the entire category key/object can be excluded to make free floating channels. Removing the roles array from either category or channel will create generic channels with your server defaults

```json
[
  {
    "name":"newSection",
    "definition":{
      "category":{
        "categoryName":"{0} Pokemon",
        "roles":[
          {
            "name":"{0}",
            "view": true,
            // Text
            "viewHistory": true,
            "send": false,
            "react": true,
            "pingEveryone": false,
            "embedLinks": false,
            "attachFiles": false,
            "sendTTS": false,
            "externalEmoji": false,
            "externalStickers": false,
            "createPublicThreads": false,
            "createPrivateThreads": false,
            "sendThreads": false,
            "slashCommands": false,
            // Voice
            "connect": false,
            "speak": false,
            "autoMic": false,
            "stream": false,
            "vcActivities": false,
            "prioritySpeaker": false,
            // Admin
            "createInvite": false,
            "channels": false,
            "messages": false,
            "roles": false,
            "webhooks": false,
            "threads": false,
            "events": false,
            "mute": false,
            "deafen": false,
            "move": false
          },
          {
            "name":"@everyone",
            "view": false
          }
        ]
      },
      "channels":[
        {
          "channelName":"{0}-chatroom",
          "channelType":"text",
          "topic": "Chat for {0} users",
          "roles":[
            {
              "name":"{0}",
              "view": true,
              "viewHistory": true,
              "send": true,
              "react": true,
              "externalEmoji": true
            },
            {
              "name":"{1}",
              "view": true,
              "viewHistory": true,
              "send": false,
              "react": true
            },
            {
              "name":"@everyone",
              "view": false
            }
          ]
        },
        {
          "channelName":"{0}-voicechat",
          "channelType":"voice",
          "topic": "Voice for {0}"
        },
        {
          "channelName":"{0}-iv100",
          "channelType":"text",
          "topic": "Hundos for {0}",
          "controlType":"bot",
          "commands":[
            "area add {0}",
            "track everything iv100"
          ]
        },
        {
          "channelName":"{0}-UltraRares",
          "channelType":"text",
          "controlType":"webhook",
          "commands":[
            "area add {0}",
            "track everything rarityultra-rare maxrarityultra-rare"
          ]
        },
        {
          "channelName":"{0}-Raids",
          "channelType":"text",
          "controlType":"webhook",
          "commands":[
            "area add {0}",
            "egg level5 level6 clean",
            "raid level5 level6 clean"
          ]
        }
      ]
    }
  }
]
```
