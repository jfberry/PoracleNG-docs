---
layout: home
title: Home
nav_order: 1
has_toc: true
---

![logo](assets/PoracleJSWiki.png)
# Poracle Documentation

PoracleJS is a high performance notification system that works with MAD or RDM.  It can send 
end-user configurable alerts to users as well as populating channels with information about Pokemon Go.

It supports sending messages about wild spawns, eggs, raids, field research (quests), invasions, lures, and nests

Some features include:
* Support for Discord and Telegram (simultaneously if required)
  * Discord - supports direct messages to users, bot-based channel posting, and webhooks
  * Telegram - supports direct messages to users, group based posting, and channels
* Support for RDM and MAD
* Web user interface to allow end-users to configure tracking (PoracleWeb)
* High performance - some users handling more than 20M events per day
    * Full rate management delivery control with per-route back-off
    * Automatic user timeout and suspension for users who track too broadly
* User subscriptions can track by area or distance from location - with multiple subscription categories by
  different distances (eg Pikachu IV90+ 250m from my house, IV100 within areas, level 5 raids within 100m)
* Automatic deleting of out-of-date messages
* Fully configurable alert styles, including multiple template styles for different uses
* Multiple discord servers
* Fully multi-lingual. Poracle has translations for most major languages and can be configured to allow end-users
    to switch language.
* User switchable profiles to support different tracking requirements - eg home and office; with auto-switching based on 
  time/day of week
* Support for custom map tiles, including pre-generation of tiles and "multi-maps" (eg including both map overview and satellite imagery)
* In game weather forecast, and notification when weather changes affect tracked Pokemon
* Multiple community (area) security allowing membership to different areas with different security gates (discord role
  or telegram group membership)
* Templated creation of alert-channels to allow easy support of new areas
* Rarity classification and dynamic filtering of alerts by rarity

