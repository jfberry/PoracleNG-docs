---
title: Customize Alarm Messages
nav_order: 4
layout: default
parent: Configuration
---

{% raw %}
# Custom Messages

One of Poracle's greatest strengths is its ability to deliver a very configurable set of alarm
messages.  These are configured through the dts files.

The main dts configuration is stored in `./config/dts.json` - and the latest example 'starting'
dts is in `./config/defaults/dts.json`. If you don't have a dts file in your config directory, this will
be copied there to get you started.

The complete DTS will be this file, plus all files in the ./config/dts folder.  This enables
you to split our your DTS into files for individual editting - for example, by language or
by type.  There are some example additional languages, and some help text in `./config/defaults/dts`

## Anatomy of a DTS entry

```json
{
  "id": 1,
  "type": "monster",
  "language": "en",
  "default": true,
  "platform": "discord",
  "template": {
    
  }
}
```
The `type` can be one of the following

| type | Description |
|------|-------------|
| monster | Alert template for pokemon with IV |
| monsterNoIv | Alert template for pokemon without IV |
| egg | 
| raid | 
| quest | Alert template for quests |
| greeting |
| help |
| weatherchange | Sent to users tracking pokemon affected by a weather change |
| nest |

The `platform` should be one of **discord** or **telegram**.
Language is used under multi-lingual configuration to support different 
templates for each language. If you have just a single language just make sure
this matches your configured locale, or leave the language entry out. Poracle will match
DTS entries with matching language in preference to no-language, but then fall back to ones with
no-language.

The `id` is the template id. When a user or administrator sets up a new
alarm, the default id is set - which is usually '1' (this can be changed
in your config file).   This can be overridden on the track command eg `!track
everything iv100 templatehundo` - this would try to pick a template with an id
of "hundo".  The first entry with `default` true will be used if a template
can't be matched exactly.

## dts.json

### Discord
A useful visualizer can be found [HERE](https://leovoel.github.io/embed-visualizer/) 

TODO: insert picture of an alert explaining sections

### Telegram

```json
    "template": {
      "content": "...",
      "sticker": "...",
      "location": true,
      "webpage_preview": true
    }
```

| content | This is where the main message content is |
| sticker | This is a URL for a telegram sticker that will be included ahead of the message |
| location | true/false - whether to include a telegram map |
| webpage_preview | true/false - whether to provide previews of embedded URL elements |
| send_order | can vary the order of the messages poracle sends |

Since handlebars was originally designed for use on web pages, when you use a field
eg {{name}} it will encode characters that need special treatment on web pages - eg
<, >, &.  You can avoid this by using three brackets - eg {{{name}}}

Sometimes it's necessary to use three curly braces on each side. This avoids url encoding for fields that need an url. You can do all kinds of fancy handlebar magic, read more about that [HERE][https://github.com/helpers/handlebars-helpers].

By default, Poracle sends messages in the order `sticker`, `text`, `location`.  You can
override this by using the `send_order` option:

```json
      "send_order": ["sticker","location","text"]
```

### Monster alarms

```json
{
    "id": 1,
    "type": "monster",
    "default": true,
    "platform": "discord",
    "template": {
      "embed": {
        "color": "{{ivcolor}}",
        "title": "{{round iv}}% {{name}} cp:{{cp}} L:{{level}} {{atk}}/{{def}}/{{sta}} {{boostemoji}}",
        "description": "End: {{time}}, Time left: {{tthm}}m {{tths}}s \n {{addr}} \n quick: {{quickMove}}, charge {{chargeMove}} \n Maps: [Google]({{{mapurl}}}) | [Apple]({{{applemap}}})",
        "thumbnail": {
          "url": "{{{imgUrl}}}"
        }
      }
    }
  },
```

For monsters without IV information, you can specify a different message.

```json
{
    "id": 1,
    "type": "monsterNoIv",
    "default": true,
    "platform": "discord",
    "template": {
      "embed": {
        "color": "{{color}}",
        "title": "?% {{name}} {{boostemoji}}",
        "description": "Ends: {{time}}, Time left: {{tthm}}m {{tths}}s \n {{addr}} \n Maps: [Google]({{{mapurl}}}) | [Apple]({{{applemap}}})",
        "thumbnail": {
          "url": "{{{imgUrl}}}"
        }
      }
    }
  },
```

Any of the fields can be customized with the following:

| Option                 | Value                                                                                                  | 
|------------------------|:-------------------------------------------------------------------------------------------------------|
| {{pokemonId}} {{id}}   | Pokemon Id                                                                                             |
| {{name}}               | Pokemon name (localised)                                                                               |
| {{nameEng}}            | Name of pokemon (english)                                                                              |
| {{disguisePokemonName}} | Name of pokemon disguise (if ditto)|
| {{encounterId}}        | The encounter id from the scanner                                                                      |
| {{generation}}         | generation number                                                                                      |
| {{generationName}}     | generation name (translated, Eng version available)                                                    |
| {{generationNameEng}}  | generation name (English)                                                                              |
| {{generationRoman}}    | roman numeral version (eg I, II, III)                                                                  |
| {{formName}}           | Monsters form (localised)                                                                              |
| {{formNameEng}}        | Monsters form (english)                                                                                |
| {{formId}}             | Form id                                                                                                |
| {{time}}               | Disappear time                                                                                         |
| {{tthh}}               | Full hours until hidden                                                                                |
| {{tthm}}               | Full minutes until hidden                                                                              |
| {{tths}}               | Full seconds until hidden                                                                              |
| {{confirmedTime}}      | True/False if disappear timestamp is verified.                                                         |
| {{now}}                | Current Timestamp                                                                                      |
| {{latitude}}           | Latitude of the alerted location                                                                       |
| {{longitude}}          | Longitude of the alerted location                                                                      |
| {{addr}}               | String ddress of the alerted location                                                                  |
| {{streetNumber}}       | Street number of the alerted location                                                                  |
| {{streetName}}         | Street name of the alerted location                                                                    |
| {{zipcode}}            | Zip code of the alerted location                                                                       |
| {{country}}            | Country of the alerted location                                                                        |
| {{countryCode}}        | 2 letter country code of the alerted location                                                          |
| {{city}}               | City name of the alerted location                                                                      |
| {{state}}              | State name of the alerted location                                                                     |
| {{stateCode}}          | 2 letter state code of the alerted location                                                            |
| {{neighbourhood}}      | Neighbourhood of the alerted location                                                                  |
| {{distance}}           | Distance in metres from the set position to the alerted location                                       |
| {{bearing}}            | Bearing from the set position to the alerted location                                                  |
| {{bearingEmoji}}       | Bearing emoji from the set position to the alerted location                                            |
| {{quickMoveId}}        | Monsters quick move (alt: {{moveName move_1}} )                                                        |
| {{quickMoveName}}      | Monster's quick move name (localised)                                                                  |
| {{quickMoveNameEng}}   | Monster's quick move name (english)                                                                    |
| {{quickMoveEmoji}}     | Monster's quick move emoji                                                                             |
| {{chargeMoveId}}       | Charge move id                                                                                         |
| {{chargeMoveName}}     | Monsters charge move (localised)                                                                       |
| {{chargeMoveNameEng}}  | Monsters charge move (english)                                                                         |
| {{iv}}                 | Monsters Individual Value Precentage (2sp)                                                             |
| {{ivColor}}            | Color code that reflects this IV                                                                       |
| {{cp}}                 | Monsters CP                                                                                            |
| {{genderData.name}}    | Monsters gender                                                                                        |
| {{genderData.emoji}}   | Monsters gender emoji                                                                                  |
| {{gender}}             | Gender id number; 0 = unset, 1 = male, 2 = female, 3 = genderless                                      |
| {{weight}}             | Monsters weight in kg                                                                                  |
| {{height}}             | Monster's height                                                                                       |
| {{level}}              | Monsters level                                                                                         |
| {{atk}}                | Monsters attack                                                                                        |
| {{def}}                | Monsters defense                                                                                       |
| {{sta}}                | Monsters stamina                                                                                       |
| {{{staticMap}}}        | Static link to map                                                                                     |
| {{{googleMapUrl}}}     | Link to google maps search of the location                                                             |
| {{{appleMapUrl}}}      ||                                                                                             |
| {{{waveMapUrl}}}       ||                                                                                             |
| {{{imgUrl}}}           | Link to monsters picture                                                                               |
| {{{stickerUrl}}        | Link to monsters sticker                                                                               |
| {{color}}              | Color to be used for embed (Color of monsters primary type)                                            |
| {{ivColor}}            | Color to be used for embed (Color of monsters perfection based on config.discord.iv_colors)            |
| {{flag}}               | Country flag emoji for location                                                                        |
| {{areas}}              | Matched geofence area names for alert                                                                  |
| matched                | Matched areas (array)                                                                                  |
| weatherCurrent         ||                                                                                             |
| alteringWeathers       ||                                                                                             |
| weatherChangeTime      | Time weather will change                                                                               |
| boosted                | Is Pokemon weather boosted?                                                                            |
| boostWeather           |                                                                                                        |
| boostWeatherName       | Name of weather boost                                                                                  |
| boostWeatherEmoji      | Emoji of weather boost                                                                                 |
| gameWeather            |                                                                                                        |
| gameWeatherName        | Current game weather name                                                                              |
| gameWeatherEmoji       | Current game weather emoji                                                                             |
| weatherChange          | Weaher change text indicating possible weather change                                                  |
| weatherCurrentName     | Current weather name if there is a forecast change                                                     |
| weatherCurrentEmoji    | Emoji for current weather                                                                              |                                                                                             |
| weatherNextName        | Name of next weather if there is a forecast change                                                     |
| weatherNextEmoji       | Emoji of next weather if there is a forecast change                                                    |
| bestGreatLeagueRank    ||                                                                                             |
| bestGreatLeagueRankCP  ||                                                                                             |
| bestUltraLeagueRank    ||                                                                                             |
| bestUltraLeagueRankCP  ||                                                                                             |
| pvpDisplayMaxRank      ||                                                                                             |
| pvpDisplayGreatMinCP   ||                                                                                             |
| pvpDisplayUltraMinCP   ||                                                                                             |
| {{catchBase}}          | Catch base %age                                                                                        |
| {{catchGreat}}         | Catch great ball %age                                                                                  |
| {{catchUltra}}         | Catch ultra ball %age                                                                                  |
| {{rarityNameEng}       | Rarity of pokemon (english)                                                                            |
| {{rarityName}}         | Rarity of pokemon (localised)                                                                          |
| {{typeName}}           | Type of pokemon (localised)                                                                            |
| {{emojiString}}        | Emoji of pokemon type                                                                                  |
| {{shinyPossible}}      | true/false Whether this pokemon is shiny possible                                                      |
| {{shinyPossibleEmoji}} | An emoji indicating whether shiny is possible                                                          |

Some examples:
```
{{name}}{{#if form}}{{#isnt formname 'Normal'}} {{formname}}{{/isnt}}{{/if}}
```

```
{{#if confirmedTime}}Verified{{else}}Not verified{{/if}}`
```

```
{{name}}{{#if disguisePokemonName}} (disguised as {{disguisePokemonName}}){{/if}}
```
#### PVP 

```
{{#if pvpLittle}}**Little league:**\n
{{#each pvpLittle}}- {{this.fullName}} #{{this.rank}} @{{this.cp}}CP (Lvl. {{this.level}})\n
{{/each}}{{/if}}{{#if pvpGreat}}**Great league:**\n
{{#each pvpGreat}} - {{this.fullName}} #{{this.rank}} @{{this.cp}}CP (Lvl. {{this.level}})\n
{{/each}}{{/if}}{{#if pvpUltra}}**Ultra League:**\n
{{#each pvpUltra}} - {{this.fullName}} #{{this.rank}} @{{this.cp}}CP (Lvl. {{this.level}})\n
{{/each}}{{/if}}
```

These fields can be used inside a PVP loop:

| field        | description                                                                     |
|--------------|---------------------------------------------------------------------------------|
| fullName     | a full name including form (and mega)                                           |
| fullNameEng  | english version                                                                 |
| rank         ||
| level        ||
| cap          | (the cap of this entry, eg 50 or 51)                                            |
| levelWithCap | a combination field (eg 50/50) showing level and cap if the mon is not 'capped' |
| formId       |
| pokemonId    |
| evolution    | true/false (whether this is a mega or not)                                      |
| cp           |
| percentage   | (2dp)                                                                           |
| monsterName  | - just the pokemon name                                                         |
| formName     | - just the form name                                                            |
| baseStats    | - for quick use of calculateCp                                                  |

### Raid alarms

```json
  {
    "id": 1,
    "type": "raid",
    "default": true,
    "platform": "discord",
    "template": {
      "embed": {
        "title": "Raid against {{name}} has started at {{gymName}}!",
        "description": "CP: {{cp}}, quick: {{quickMove}}, charge {{chargeMove}} \n Maps: [Google]({{{mapurl}}}) | [Apple]({{{applemap}}})",
        "color": "{{color}}",
        "thumbnail": {
          "url": "{{{imgUrl}}}"
        },
        "author": {
          "name": "{{name}} lvl{{level}}. End: {{time}} in {{tthm}}m {{tths}}s",
          "icon_url": "{{{detailsurl}}}"
        }
      }
    }
  },
```

| Option        | Value         | 
|---------------|:-------------:|
|{{pokemonId}}<br>{{id}}| Pokémon id|
|{{name}}| Monsters name|
|{{{gymName}}}|Name of gym|
|{{level}}| Raid level|
|{{time}}| Disappear time|
|{{formId}} | Form Id |
|{{formName}} | Form name |
|{{tthh}}| Full hours until raid ends|
|{{tthm}}| Full minutes until raid ends|
|{{tths}}| Full seconds until raid ends|
|{{now}}| Current Timestamp|
|{{hatchTime}} | Hatch time of egg|
|{{latitude}}| Latitude of the alerted location|
|{{longitude}}| Longitude of the alerted location|
|{{addr}}| Address of the alerted location|
|{{streetNumber}}| Street number of the alerted location|
|{{streetName}}| Street name of the alerted location|
|{{zipcode}}| Zip code of the alerted location|
|{{country}}| Country of the alerted location|
|{{countryCode}}| 2 letter country code of the alerted location|
|{{city}}| City name of the alerted location|
|{{state}}| State name of the alerted location|
|{{stateCode}}| 2 letter state code of the alerted location|
|{{neighbourhood}}| Neighbourhood of the alerted location|
|{{quickMoveName}}| Monsters quick move |
|{{chargeMoveName}}| Monsters charge move|
|{{quickMoveEmoji}}| Monsters quick move type emoji|
|{{chargeMoveEmoji}}| Monsters charge move type emoji|
|{{cp}}| Raid boss cp|
|{{calculateCp baseStats 20 15 15 15}}| Monsters cp with 100% perfect IV and level 20|
|{{calculateCp baseStats 25 15 15 15}}| Monsters cp with 100% perfect IV and level 25|
|{{calculateCp baseStats 20 0 0 0}}| Monsters cp with 0% perfect IV and level 20|
|{{calculateCp baseStats 25 0 0 0}}| Monsters cp with 0% perfect IV and level 25|
|{{teamName}}| Team owner of the gym |
|{{description}}| Description of the gym|
|{{{gymUrl}}}| Descriptive picture url|
|{{{staticMap}}}| Static link to map|
|{{{googleMapUrl}}}|Link to google maps search of the location|
|{{{appleMapUrl}}}|Link to apple maps search of the location|
|{{{wazeMapUrl}}}|Link to waze maps search of the location|
|{{{imgUrl}}}| Link to monsters picture|
|{{gymColor}}| Color to be used for embed (Color of monsters primary type)|
|{{ex}}| If raid takes place in a potential EX gym (empty string if false)|
|{{#if ex}}True{{else}}False{{#if}}| Prints True if ex eligible, False if not|
|{{flag}}Country flag emoji for location|
|{{areas}}| Matched geofence area names for alert|


### Egg alarms

```json
 {
    "id": 1,
    "type": "egg",
    "default": true,
    "platform": "discord",
    "template": {
      "embed": {
        "title": "Raid egg level{{level}} at {{gymName}}",
        "description": "Maps: [Google]({{{mapurl}}}) | [Apple]({{{applemap}}})",
        "color": "{{color}}",
        "thumbnail": {
          "url": "{{{detailsurl}}}"
        },
        "author": {
          "name": "Hatch at: {{time}} in {{tthm}}m {{tths}}s",
          "icon_url": "{{{imgUrl}}}"
        }
      }
    }
  },
```



| Option        | Value         | 
|---------------|:-------------:|
|{{{gymName}}}| Name of the gym|
|{{level}}| Raid level|
|{{hatchTime}| Time of hatching|
|{{time}}| Disappear time|
|{{tthh}}| Full hours until raid ends|
|{{tthm}}| Full minutes until raid ends|
|{{tths}}| Full seconds until raid ends|
|{{now}}| Current Timestamp|
|{{addr}}| Address of the alerted location|
|{{latitude}}| Latitude of the alerted location|
|{{longitude}}| Longitude of the alerted location|
|{{streetNumber}}| Street number of the alerted location|
|{{streetName}}| Street name of the alerted location|
|{{zipcode}}| Zip code of the alerted location|
|{{country}}| Country of the alerted location|
|{{countryCode}}| 2 letter country code of the alerted location|
|{{city}}| City name of the alerted location|
|{{state}}| State name of the alerted location|
|{{stateCode}}| 2 letter state code of the alerted location|
|{{neighbourhood}}| Neighbourhood of the alerted location|
|{{teamName}}| Team owner of the gym |
|{{teamEmoji}}| Team emoji|
|{{description}}| Description of the gym|
|{{{detailsurl}}}| Descriptive picture url|
|{{{staticmap}}}| Static link to map|
|{{{mapurl}}}|Link to google maps search of the location|
|{{{imgUrl}}}| Link to monsters picture|
|{{color}}| Color to be used for embed (Color of monsters primary type)|
|{{ex}}| If raid takes place in a potential EX gym (empty string if false|
|{{#ex}}True{{/ex}}{{^ex}}False{{/ex}}| Prints True if ex eligible, False if not|
|{{flag}}|Country flag emoji for location|
|{{gameWeatherNameEng}}|
|{{gameWeatherName}|

### Quest alarms

```json
  {
    "id": 1,
    "type": "quest",
    "default": true,
    "platform": "discord",
    "template": {
      "embed": {
        "title": "{{questType}} \n Pokestop Name: {{pokestop_name}}",
        "url": "{{{mapurl}}}",
        "description": "Conditions: {{conditionString}} \n Reward: {{rewardString}} {{monsterNames}} \n {{addr}} \n Maps: [Google]({{{mapurl}}}) | [Apple]({{{applemap}}})",
        "thumbnail": {
          "url": "{{{imgUrl}}}"
        }
      }
    }
  },
```



| Option        | Value         | 
|---------------|:-------------:|
|{{now}}| Current Timestamp|
|{{questString}}| The type of quest (for example: battle in 3 raids)|
|{{rewardString}}| Reward if you finish (Pokemon, item or stardust)|
|{{monsterNames}}| Names of all reward monsters for this quest|
|{{itemNames}}| Names of all reward Items for this quest|
|{{dustAmount}}| The word "stardust" if it's that type of quest|
|{{isShiny}}| Pokemon reward is shiny|
|{{itemAmount}}| |
|{{energyAmount}} | |
|{{energyMonstersNames}}| |
|{{{imgUrl}}}| Image of the reward. Could be Pokemon or Item or stardust|
|{{{stickerUrl}}}| Image of the reward. Could be Pokemon or Item or stardust|
|{{pokestopName}}| Name of the Pokestop|
|{{pokestopUrl}}| Name of the Pokestop|
|{{{staticMap}}}| Static link to map|
|{{{googleMapUrl}}}| Link to google maps|
|{{{appleMapUrl}}}| Link to apple maps|
|{{{wazeMapUrl}}}| Link to apple maps|
|{{areas}}| Matched geofence area names for alert|
|{{latitude}}| Latitude of the alerted location|
|{{longitude}}| Longitude of the alerted location|
|{{streetNumber}}| Street number of the alerted location|
|{{streetName}}| Street name of the alerted location|
|{{zipcode}}| Zip code of the alerted location|
|{{country}}| Country of the alerted location|
|{{countryCode}}| 2 letter country code of the alerted location|
|{{neighbourhood}}| Neighbourhood of the alerted location|
|{{city}}| City name of the alerted location|
|{{state}}| State name of the alerted location|
|{{stateCode}}| 2 letter state code of the alerted location|
|{{flag}}|Country flag emoji for location|

### Invasion alarms

```json
  {
    "id": 1,
    "type": "invasion",
    "default": true,
    "platform": "discord",
    "template": {
      "embed": {
        "title": "Team Rocket at {{name}}!",
        "url": "{{{mapurl}}}",
        "description": "Name: {{gruntName}} {{genderName}} \n Type: {{gruntType}} {{gruntTypeEmoji}} \n Possible Rewards: {{gruntRewards}} \n Ends: {{time}}, in {{tthm}}m {{tths}}s \n Maps: [Google]({{{mapurl}}}) | [Apple]({{{applemap}}})",
        "thumbnail": {
          "url": "{{{imgUrl}}}"
        }
      }
    }
  },
```


| Option        | Value         | 
|---------------|:-------------:|
|{{{pokestopName}}}| Name of the Pokestop|
|{{gruntName}}| The name of the grunt (grunt female or grunt male)|
|{{disappearTime}}| Invasion end time|
|{{tthh}}| Full hours until invasion ends|
|{{tthm}}| Full minutes until invasion ends|
|{{tths}}| Full seconds until invasion ends|
|{{now}}| Current Timestamp|
|{{gruntType}}| The type of invasion (for example: Rock, Mixed|
|{{gruntTypeColor}}| Color representing type of invasion|
|{{gruntTypeEmoji}}| The emoji of type of invasion|
|{{gruntRewards}}| If known, a list of possible rewards. Splits into 2 lines if there's a 85/15 split chance of reward.|
|{{gruntRewardsList}}| A list of possible rewards containing chance, id and name|
|{{genderData.name}}|
|{{genderData.emoji}}|
|{{gruntTypeId}}| The id of the invasion type|
|{{gender}}| The id of the grunt gender|
|{{{imgUrl}}}| alias for Link to the image of the Pokestop|
|{{pokestopUrl}}| Link to the image of the Pokestop|
|{{{staticMap}}}| Static link to map|
|{{{googleMapUrl}}}| Link to google maps|
|{{{appleMapUrl}}}| Link to apple maps|
|{{{wazeMapUrl}}| Link to waze|
|{{areas}}| Matched geofence area names for alert|
|{{latitude}}| Latitude of the alerted location|
|{{longitude}}| Longitude of the alerted location|
|{{addr}}|Address|
|{{streetNumber}}| Street number of the alerted location|
|{{streetName}}| Street name of the alerted location|
|{{zipcode}}| Zip code of the alerted location|
|{{country}}| Country of the alerted location|
|{{countryCode}}| 2 letter country code of the alerted location|
|{{neighbourhood}}| Neighbourhood of the alerted location|
|{{city}}| City name of the alerted location|
|{{state}}| State name of the alerted location|
|{{stateCode}}| 2 letter state code of the alerted location|
|{{flag}}|Country flag emoji for location|

```
Possible rewards: {{#compare gruntRewardsList.first.chance '==' 100}}{{#forEach gruntRewardsList.first.monsters}}{{this.name}}{{#unless isLast}}, {{/unless}}{{/forEach}}{{/compare}}{{#compare gruntRewardsList.first.chance '<' 100}}\n ‣ {{gruntRewardsList.first.chance}}% : {{#forEach gruntRewardsList.first.monsters}}{{this.name}}{{#unless isLast}}, {{/unless}}{{/forEach}}\n ‣ {{gruntRewardsList.second.chance}}% : {{#forEach gruntRewardsList.second.monsters}}{{this.name}}{{#unless isLast}}, {{/unless}}{{/forEach}}{{/compare}}
```


### Weather

```json
  {
    "id": 1,
    "type": "weatherchange",
    "default": true,
    "platform": "discord",
    "template": {
      "embed": {
        "title": "⚠️ Weather change ⚠️",
        "description": "The weather for some active Pokémons you are tracking has changed!\n{{#if oldweather}}It went from {{oldweather}} {{oldweatheremoji}} to {{else}}It is now {{/if}}{{weather}} {{weatheremoji}}\n{{#if activePokemons}}The following Pokémons have now changed and will have different stats:\n{{#each activePokemons}}**{{this.name}}** {{#isnt this.formname 'Normal'}} {{this.formname}}{{/isnt}} - {{round this.iv}}% - {{this.cp}}CP\n{{/each}}{{else}}This could have altered the reported stats and IV...{{/if}}"
      }
    }
  },
```


| Option        | Value         | 
| --------------- |:-------------:
|{{latitude}}| Latitude of the alerted location|
|{{longitude}}| Longitude of the alerted location|
|{{oldweather}}| Weather of the past hour|
|{{oldweatheremoji}}| Weatheremoji of the past hour|
|{{weather}}| Weather of the current hour|
|{{weatheremoji}}| Weatheremoji of the current hour|
|{{weatherChange}}| an already formed sentence for this weather change|
|{{weatherCurrent}}| current weather ID|
|{{weatherCurrentName}}| current weather|
|{{weatherCurrentEmoji}}| current weather emoji|
|{{weatherNext}}| weather ID forecast for next hour|
|{{weatherNextName}}| weather name for next hour|
|{{weatherNextEmoji}}| weather emoji for next hour|
|{{weatherChangeTime}}| change time (format HH:00)|

### Lures

```json5
  {
      "id": 1,
      "language": "en",
      "type": "lure",
      "default": true,
      "platform": "discord",
      "template": {
        "username": "{{lureTypeName}}",
        "avatar_url": "https://raw.githubusercontent.com/nileplumb/PkmnHomeIcons/master/RDM_OS_128/pokestop/{{minus lureTypeId 500}}.png",
        "embed": {
          "title": "Lure at {{{pokestopName}}}",
          "url": "{{{googleMapUrl}}}",
          "color": "{{lureTypeColor}}",
          "description": "**Type:** {{lureTypeName}}\n **Ends at:** {{time}} \n(**Time left:** {{#if tthh}}{{tthh}}h {{/if}}{{tthm}}m {{tths}}s)\n**Address:** {{{addr}}}\n[Google]({{{googleMapUrl}}}) | [Apple]({{{appleMapUrl}}}) | [Waze]({{{wazeMapUrl}}})",
          "thumbnail": {
            "url": "{{{imgUrl}}}"
          },
          "image": {
            "url": "{{{staticMap}}}"
          }
        }
      }
  },
```


| Option        | Value         | 
|---------------|:-------------:|
|{{{pokestopName}}}| Name of the Pokestop|
|{{lureTypeName}}| The type of lure (localised)|
|{{lureTypeNameEng}}| The type of lure (english)|
|{{lureTypeEmoji}}|Lure type emoji|
|{{lureColor}}|Color of lure|
|{{lureTypeId}}|Lure type id|
|{{disappearTime}}| Invasion end time|
|{{tthh}}| Full hours until invasion ends|
|{{tthm}}| Full minutes until invasion ends|
|{{tths}}| Full seconds until invasion ends|
|{{now}}| Current Timestamp|
|{{{imgUrl}}}| alias for Link to the image of the Pokestop|
|{{pokestopUrl}}| Link to the image of the Pokestop|
|{{{staticMap}}}| Static link to map|
|{{{googleMapUrl}}}| Link to google maps|
|{{{appleMapUrl}}}| Link to apple maps|
|{{{wazeMapUrl}}| Link to waze|
|{{areas}}| Matched geofence area names for alert|
|{{latitude}}| Latitude of the alerted location|
|{{longitude}}| Longitude of the alerted location|
|{{addr}}|Address|
|{{streetNumber}}| Street number of the alerted location|
|{{streetName}}| Street name of the alerted location|
|{{zipcode}}| Zip code of the alerted location|
|{{country}}| Country of the alerted location|
|{{countryCode}}| 2 letter country code of the alerted location|
|{{neighbourhood}}| Neighbourhood of the alerted location|
|{{city}}| City name of the alerted location|
|{{state}}| State name of the alerted location|
|{{stateCode}}| 2 letter state code of the alerted location|
|{{flag}}|Country flag emoji for location|


### Nests

```json
 {
    "id": 1,
    "type": "nest",
    "language": "en",
    "default": true,
    "platform": "discord",
    "template": {
      "embed": {
        "title": "{{name}} nest at {{nestName}}",
        "description": "Nest first found on {{resetDate}}\nAverage spawn: {{pokemonSpawnAvg}}/hour",
        "color": "{{color}}",
        "thumbnail": {
          "url": "{{{imgUrl}}}"
        },
        "image": {
          "url": "{{{staticMap}}}"
        }
      }
    }
  },
```

| Option        | Value         | 
| --------------- |:-------------:|
|{{{nestName}}}| Name of the Nest|
|{{pokemonId}}| Pokemon Id|
|{{nameEng}}|Name of pokemon (english)|
|{{name}|Name of Pokemon (localised)
|{{pokemonCount}||
|{{pokemonSpawnAvg}|Pokemon spawns per hour|
|{{disappearDate}}| Predicted nest end date|
|{{resetDate}}| Date when nest last reset|
|{{now}}| Current Timestamp|
|{{{imgUrl}}}| Link to pokemon image|
|{{{staticMap}}}| Static link to map|
|{{{googleMapUrl}}}| Link to google maps|
|{{{appleMapUrl}}}| Link to apple maps|
|{{{wazeMapUrl}}| Link to waze|
|{{areas}}| Matched geofence area names for alert|
|{{latitude}}| Latitude of the alerted location|
|{{longitude}}| Longitude of the alerted location|
|{{addr}}|Address|
|{{streetNumber}}| Street number of the alerted location|
|{{streetName}}| Street name of the alerted location|
|{{zipcode}}| Zip code of the alerted location|
|{{country}}| Country of the alerted location|
|{{countryCode}}| 2 letter country code of the alerted location|
|{{neighbourhood}}| Neighbourhood of the alerted location|
|{{city}}| City name of the alerted location|
|{{state}}| State name of the alerted location|
|{{stateCode}}| 2 letter state code of the alerted location|
|{{flag}}|Country flag emoji for location|


### Help messages


### Greeting Message

```json
  {
    "id": 1,
    "type": "greeting",
    "default": true,
    "platform": "discord",
    "template": {
      "embed": {
        "title": "Welcome",
        "description": "Thank you for registering \nPlease set a location `{{prefix}}location name of place` or add ares where to receive alarms from",
        "fields": [
          {
            "name": "General commands",
            "value": "`{{prefix}}poracle`: Adds you to database and enables tracking \n`{{prefix}}unregister`: Removes you from tracking \n`{{prefix}}stop`: Temporarily stops alarms \n`{{prefix}}start`: Re-enables alarms \n`{{prefix}}location yourArea`: Searches for yourArea and sets it as your location \n`{{prefix}}area add somePlace`: Sets one or multiple areas where to receive alarms from, areas need to be configured by admin \n`{{prefix}}area remove somePlace`: Removes a configured area"
          },
          {
            "name": "Monster tracking commands",
            "value": "`{{prefix}}track snorlax lapras d500 iv50 maxiv90 cp1000 level15`: Any arguments are optional, this command would alert you about snorlax and lapras within 500 meters of your location or inside an added area. The set filters require them to have IV between 50% - 90% be at least level 15 and minimum CP of 1000 \n`{{prefix}}untrack lapras vileplume`: will remove tracking for lapras and vileplume"
          },
          {
            "name": "Raid tracking commands",
            "value": "`{{prefix}}raid snorlax lapras d500 instinct`: Any arguments are optional, this command would alert you about snorlax and lapras raids within 500 meters of your location or inside an added area. The set filters require the Gym to be controlled by team Instinct \n`{{prefix}}raid remove lapras vileplume`: will remove tracking for lapras and vileplume raids"
          },
          {
            "name": "Raid egg tracking commands",
            "value": "`{{prefix}}egg level3 d500 instinct`: Any arguments are optional, this command would alert you about level 3 raid eggs within 500 meters of your location or inside an added area. The set filters require the Gym to be controlled by team Instinct \n`{{prefix}}egg remove level3`: will remove tracking for level 3 raid eggs"
          },
          {
            "name": "Quest tracking commands",
            "value": "`{{prefix}}quest porygon pikachu poke ball d500 `: Any arguments are optional, this command would alert you about Quests obtainable within 500m of your location with porygon, pikachu or pokeballs as rewards \n `{{prefix}}quest remove all items` Removes tracking for all item based quests. Can also use `all pokemon` or `stardust`"
          },
          {
            "name": "Invasion tracking commands",
            "value": "`{{prefix}}invasion template3 d500 dragon mixed`: Any arguments are optional, this command would alert you about Team Rocket Incidents within 500m of your location if the grunt type was mixed or dragon. You can use any pokemon type name.\n `{{prefix}}invasion remove` Removes tracking for all Team Rocket Incidents."
          }
        ]
      }
    }
  }
```
{% endraw %}
This is the message that is sent to newly added users via DM. There are no dynamic variables in this message.  

The "fields" without title and description are sent to users upon `!help` command

###Maps

