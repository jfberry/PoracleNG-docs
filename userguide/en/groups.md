---
title: Poracle for Groups
nav_order: 1
layout: default
parent: English
grand_parent: User Guide
---

# Poracle for Groups
This section is primarily intended for the scan initiators.

## How to Install in Groups/Channels
**Discord**: When you create a Discord bot, the bot can be assigned to a server. From there, it is ready for use and can be added to all subgroups.

**Telegram**: In Telegram, you can add the bot to however many groups/channels you like. Both the bot and the previously entered bot admins have to be entered as group/channel admin. Adjust your Telegram settings accordingly. Otherwise, the bot won't (be able to) respond.

Telegram and Discord have one big difference in the commands for the Poracle bot: In Telegram, you start all commands with "/" (Slash), while in Discord, you start with "!" (exclamation mark). The rest of the command is identical in both applications. (For Discord, replace all following / with !.) To set up a group for specific notifications, you need to do as follows:

**`/channel add`**  
Grants a new group/channel access to the bot.

**`/area add name`**  
Adds a new area that you want the bot to send you notifications for. You can select several areas at the same time.

**`/area list`**  
Is optional and shows all areas available for this Poracle bot. Once we are finished with the setup, at least one name of your area should appear here. From now on, you can enter filter settings. In the chapter [Filtering Options](#filtering-options), you can find an extended overview of options. This is just one brief example:

**`/track everything IV97`**  
From now on, you will receive notifications for all Pokémon with IVs of at least 97% in your selected area.

**`/tracked`**  
For check-up. The bot generates a list of all things you track. If the list is too long, it will be provided as a file rather than a message. We recommend always using this command after setting new filters to check that everything works as intended. Furthermore, it's helpful for us when you attach the response (including the file) to this command to any support requests regarding your filtering.

**`/help`**  
Shows a selection of commands that the bot can process and some examples. You can find a more extensive list here. 

The bot should respond to all entries meant for it. If the bot does not answer at all, there is an error in the setup. In this case, check that all previous steps have been properly completed. If the bot still does not respond, send a support request. If the bot has processed a command successfully, it will send a :white_check_mark: emoji. If the command as such is a duplicate, you will get a :ok_hand: emoji in response. If you made a mistake in your entry, and the bot cannot process your command properly, it will send you an error alert in most cases.
