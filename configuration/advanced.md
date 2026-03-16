---
title: Other Settings (Advanced)
nav_order: 6
layout: default
parent: Configuration
---
### Pokemon aliasing

The `pokemonAlias.json` file provides a mechanism for aliasing commonly mis-spelt pokemon.
It currently has an English focus, but you can update your local copy.  Contributions for
good additions will certainly be considered for more widespread distribution so please
share

### Shortened URLs

Wrap in `<S<` and `>S>` for Poracle to pass the URL through a shortener
(currently only *xxxxx* is supported)

### Changing discord emoticons

First you'll need to add your custom emoji to your server. You can file `emoji.json` which
poracle will use to look up emoji.  

```json
{
  "discord": {
    "type-grass":"<:poracle_type_grass:yourid>",
    "type-poison":"<:poracle_type_poison:yourid>",
    "type-fire":"<:poracle_type_fire:yourid>",
    "type-flying":"<:poracle_type_flying:yourid>",
    "type-water":"<:poracle_type_water:yourid>",
    "type-bug":"<:poracle_type_bug:yourid>",
    "type-normal":"<:poracle_type_normal:yourid>",
    "type-dark":"<:poracle_type_dark:yourid>",
    "type-electric":"<:poracle_type_electric:yourid>",
    "type-rock":"<:poracle_type_rock:yourid>",
    "type-ground":"<:poracle_type_ground:yourid>",
    "type-fairy":"<:poracle_type_fairy:yourid>",
    "type-fighting":"<:poracle_type_fighting:yourid>",
    "type-psychic":"<:poracle_type_psychic:yourid>",
    "type-steel":"<:poracle_type_steel:yourid>",
    "type-ice":"<:poracle_type_ice:yourid>",
    "type-ghost":"<:poracle_type_ghost:yourid>",
    "type-dragon":"<:poracle_type_dragon:yourid>",
    "weather-sunny":"<:poracle_weather_sunny:yourid>",
    "weather-rain":"<:poracle_weather_rain:yourid>",
    "weather-partly-cloudy":"<:poracle_weather_partly_cloudy:yourid>",
    "weather-cloudy":"<:poracle_weather_cloudy:yourid>",
    "weather-windy":"<:poracle_weather_windy:yourid>",
    "weather-snow":"<:poracle_weather_snow:yourid>",
    "weather-fog":"<:poracle_weather_fog:yourid>",
    "lure-normal":"<:poracle_lure_normal:yourid>",
    "lure-glacial":"<:poracle_lure_glacial:yourid>",
    "lure-mossy":"<:poracle_lure_mossy:yourid>",
    "lure-magnetic":"<:poracle_lure_magnetic:yourid>",
    "lure-rainy":"<:poracle_lure_rainy:yourid>",
    "team-mystic":"<:poracle_team_mystic:yourid>",
    "team-valor":"<:poracle_team_valor:yourid>",
    "team-instinct":"<:poracle_team_instinct:yourid>"
  }
}

```

Poracle can upload some emoji for you from your UICONS repository, and give you an example
emoji.json

```
!poracle-emoji upload - to upload emojis (skipping those already on server)
!poracle-emoji upload overwrite - to upload overwrite emojis
!poracle-emoji - to get a list of poracle emojis in poracle emoji.json format
```

You need to run this from a server channel or specify `guild123456' as a parameter

The bot can use emojis of any server it's invited to. So as long as your bot is on each server you can just have the emoji added to just one server.

### PVP Scanner

Poracle can calculate PVP values itself, if you set it to the 'internal' calculator. The default
is to obtain PVP data from webhooks, which are provided by RDM but not by MAD.

### Facebook

Facebook messenger can be used with an add-on - see third party apps.  Note that Facebook like to ban
accounts that use unofficial bots so this is only of use for low volume groups (like hundos) rather than
a full interactive experience.

### Delegated Administration

You can configure that certain users are able to make changes to the tracking in channels, groups, or on webhooks.

For discord this looks like this:  The ID number in channel tracking can be a guild (can change tracking for all channels in a guild), a category (all channels in a category), or an individual category.
The admin

```json
     "delegatedAdministration": {
             "channelTracking": {
                "794678214236438569": [ "745735260020539513" ]
            },
            "webhookTracking": {
                "poracle-test": ["726564415431770202" ]
            }
        },
```
For telegram, the channelTracking can be the name of a channel or the id number of a group.

### Reconciliation

The reconciliation that runs every <n> hours can now do even more!
If the telegram option registerOnStart is set then this is run on a /start message (which is sent automatically by the telegram client). This means that users who are in the appropriate feeder channels will be registered on a /start and don't need to do a /poracle.

```json
"reconciliation": {
    "discord": {
        "updateUserNames": false,                // Follow discord username changes
        "removeInvalidUsers": true,              // Whether users who lose roles should be de-registered
        "registerNewUsers": false,               // Whether users who are granted roles are auto-registered
        "updateChannelNames": true,              // Whether channel names are kept in sync
        "updateChannelNotes": false,             // Whether channel notes are updated to contain guild name / category
        "unregisterMissingChannels": false       // Whether channels that are deleted from discord are removed from database
    },
    "telegram": {
        "updateUserNames": false,                // Follow telegram username changes
        "removeInvalidUsers": true               // Whether users are automatically removed
    }
},
```
