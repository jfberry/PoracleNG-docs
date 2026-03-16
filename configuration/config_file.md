---
title: Config File
nav_order: 1
layout: default
parent: Configuration
---

{% raw %}
# Config file

Poracle's main configuration file is `local.json`. You will also notice a 
`default.json` in the config directory. This is updated by the developers and
should not be changed - Poracle will first look to your local.json file and then
if it can't find a setting the reasonable defaults provided by default.json

## Validating the file
After making changes to the file we should ensure it is valid JSON. If it is not valid JSON PoracleJS will not start. The default format resembles cJSON which does not have an online linter. You may be able to put it into your browsers developer tools to ensure it is valid JSON.

## Server

Thats the section about the PoracleJS listener. It's the endpoint where you send the webhooks to.

| Option        | Value         | 
| ------------- |---------------| 
| host| Interface to listen on. Leave it at `127.0.0.1` if you are sending webhooks from the same server to PoracleJS|
| port| Port to listen on |
| ipWhitelist| Array of IPs that are able to send webhooks|
| ipBlacklist| Array of IPs that are not able to send webhooks|

## General

| Option        | Value         | 
| ------------- |---------------| 
| environment | That's for development purposes. Only change it if you know what you are doing|
| alertMinimumTime | Ignore all events with a despawntime in seconds lower than this value|
| imgUrl | URL for icons used by `{{{imgUrl}}}` in DTS|
| stickerUrl | URL for icons used by `{{{stickerUrl}}}` in DTS|
| locale | Language of Poracle, available languages: `en`, `de`, `fr` and `pl`|
| weatherChangeAlert | Poracle will send a message if a reported mon will get or lose it's weatherboost. Weather webhooks are required|
| disabledCommands | Array of disabled commands|
| disablePokemon | Set to true to disable processing of mons|
| disableRaid | Set to true to disable processing of raids|
| disableInvasion | Set to true to disable processing of invasions| 
| disablePokestop | Set to true to disable processing of invasions|
| disableQuest | Set to true to disable processing of quests|
| disablePokemon | Set to true to disable processing of weather|
| roleCheckDeletionsAllowed | Set to true to delete users from the PoracleJS database when they lose access|

## Logger

| Option        | Value         | 
| ------------- |---------------|
| level | Loglevel of Poracle|
| logSize | Max size in megabytes of the logfile, if the size is exceeded then a new file is created, a counter will become a suffix of the log file |

## Database

| Option        | Value         | 
| ------------- |---------------|
| client | Database type. Available options are: `mysql` for MySQL or MariaDB
| conn | Database credentials.

## Locale

| Option        | Value         | 
| ------------- |---------------|
| timeformat | Locale of the displayed time. You can find a list of available locales with `ls -l node_modules/moment/locale/` |
| time | Timeformat. Learn more about it in the "Locale aware formats" section [here](https://momentjs.com/docs/#/parsing/string-format/) |
| addressFormat | Formatting of `{{addr}}` in DTS |
| language | Language when using `{{translateAlt}}` in DTS |


## Geofence

| Option        | Value         | 
| ------------- |---------------|
| path | Path to the geofence.json that should be used by PoracleJS |
| defaultGeofenceName | Only applies to GEOjson type geofences, used if the name missing |
| defaultGeofenceColor | Not used at the moment|

## PVP

| Option        | Value         | 
| ------------- |---------------|
| pvpDisplayMaxRank | Limits the results shown in user alerts |
| pvpDisplayGreatMinCP |Limits the results shown in user alerts |
| pvpDisplayUltraMinCP | Limits the results shown in user alerts |
| pvpFilterMaxRank | Forces boundaries on the user tracking requests when they are out of range |
| pvpFilterGreatMinCP | Forces boundaries on the user tracking requests when they are out of range |
| pvpFilterUltraMinCP | Forces boundaries on the user tracking requests when they are out of range |

## Tracking

| Option        | Value         | 
| ------------- |---------------|
| disableEverythingTracking | Disable the option to track every mon by using `everything` |
| forceEverythingSeparately | Forces one database row for each mon instead of one row for everything |
| defaultDistance | Distance in meters when `d` isn't used |
| maxDistance | Maximum distance a user can set |

## Discord

| Option        | Value                                                                                                                                            | 
| ------------- |--------------------------------------------------------------------------------------------------------------------------------------------------|
| enabled | Enables Discord support                                                                                                                          |
| checkRole | Enables a rolecheck to automatically unregister left members                                                                                     |
| checkRoleInterval | Interval in hours when to check users                                                                                                            |
| unknownResponse | Unused option                                                                                                                                    |
| invite | Unused option                                                                                                                                    |
| token | Array of Discord bottokens. In the past you might have used multiple to balance the load of messages, but today you should just use a single bot |
| guilds | Array of Discord servers to check the members of                                                                                                 |
| channels | Array of channel IDs where users can register in                                                                                                 |
| userRole | Array of role IDs where users are automatically registered with                                                                                  |
| admins | Array of IDs of administrators                                                                                                                   |
| prefix | Symbol to use as command prefix                                                                                                                  |
| ivColors | Array of hexcolors                                                                                                                               |
| dmLogChannelID | Channel ID for user command logging                                                                                                              |
| dmLogChannelDeletionTime| Time in minutes after which the logged user commands are deleted                                                                                 |

### IV colors

There must be 6 colors defined, from worst IV to best.

The Colors are defined as you would in a html or css file: #rrggbb eg: #ff0000 for red.

The tiers of IV colors are as follows:

  | Min IV      | Max IV      | Default color  |
  | ----------- | ----------- | -------------- |
  | 0 %         | 24.9 %      | Gray   #9D9D9D |
  | 25 %        | 49.9 %      | White  #FFFFFF |
  | 50 %        | 81.9 %      | Green  #1EFF00 |
  | 82 %        | 89.9 %      | Blue   #0070DD |
  | 90 %        | 99.9 %      | Purple #A335EE |
  | 100 %       | 100 %       | Orange #FF8000 |

## Telegram

| Option        | Value         | 
| ------------- |---------------|
| enabled | Enables Telegram support |
| channels | Array of chat IDs (groups)where where users can register in |
| checkRole | Enables a member check in the channels to automatically unregister left members |
| checkRoleInterval | Interval in hours when to check users |
| token | Bot-token from @Botfather. Only one Possible|
| admins | Array of IDs of administrators |

## Geocoding 

Read more about [Geocoding](geocoding.html) and [Staticmaps](staticmaps.html).

| Option        | Value         | 
| ------------- |---------------|
| provider | Geocoding provider. Possible options are: `poracle` (not working at the moment), `nominatim` (for a selfhosted nominatim instances), `google` and `openstreetmap` (public nominatim instance, has a pretty low ratio limit) |
| providerURL | URL pointing to a nominatim instance. Not needed when using `google` or `openstreetmap`. Set it to `none` to disable it |
| geocodingKey | Google Geocoding Key if using `google` |
| staticProvider | Staticmap provider. Possible options are:  `tileservercache` (for a selfhosted tileservercache instance), `google`, `osm` (using mapquest API), `mapbox` (using mapbox API). Set it to `none` to disable it|
| staticKey | API key for `google`, `osm` or `mapbox` |
| width | Width of the staticmap |
| height | Height of the staticmap |
| zoom | Zoom of the staticmap |
| spriteHeight | Icon height on the staticmap |
| spriteWidth | Icon width on the staticmap |
| scale | Scaling of the staticmap |
| type | Styletype of the staticmap |

{% endraw %}
