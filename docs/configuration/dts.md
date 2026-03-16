# Alert Templates (DTS)

DTS (Dynamic Template System) controls how alert messages are formatted for Discord and Telegram. Templates use [Handlebars](https://handlebarsjs.com/) syntax.

## Template Files

Templates are defined in `config/dts.json`. If this file doesn't exist, the bundled default from `fallbacks/dts.json` is used.

Additional DTS files can be placed in `config/dts/` — they are merged with `dts.json`.

To customize templates, copy from `examples/` to `config/`:

```sh
cp examples/dts.json config/dts.json
```

## Structure

The DTS file is a JSON object with entries for each alert type and platform:

```json
{
  "pokemon": {
    "discord": {
      "1": {
        "embed": { ... },
        "content": "..."
      }
    },
    "telegram": {
      "1": {
        "message": "...",
        "pokemon_sticker": "..."
      }
    }
  },
  "raid": { ... },
  "egg": { ... },
  "quest": { ... },
  "invasion": { ... },
  "lure": { ... },
  "nest": { ... },
  "weather": { ... },
  "gym": { ... }
}
```

### Template Numbers

Each alert type can have multiple numbered templates (e.g. `"1"`, `"2"`, `"3"`). Users select which template to use when setting up tracking:

```
!track pikachu template2
```

The default template is controlled by `[general] default_template_name`.

## Alert Types

| Type | Trigger |
|------|---------|
| `pokemon` / `monster` | Wild pokemon spawn |
| `raid` | Active raid boss |
| `egg` | Raid egg (boss unknown) |
| `quest` | Field research quest |
| `invasion` | Team Rocket invasion |
| `lure` | Lure module on pokestop |
| `nest` | Nest pokemon change |
| `weather` | Weather change |
| `gym` | Gym team/details change |

## Common Variables

These variables are available across most alert types:

| Variable | Description |
|----------|-------------|
| `{{Pokemon name}}` | Pokemon name in user's locale |
| `{{Pokemon name alt}}` | Pokemon name in secondary language |
| `{{lat}}` | Latitude |
| `{{lon}}` | Longitude |
| `{{addr}}` | Geocoded address |
| `{{streetName}}` | Street name |
| `{{streetNumber}}` | Street number |
| `{{city}}` | City name |
| `{{state}}` | State/province |
| `{{country}}` | Country |
| `{{areas}}` | Matching geofence names |
| `{{mapUrl}}` | Static map image URL |
| `{{imgUrl}}` | Pokemon/item/gym image URL |
| `{{imgUrlAlt}}` | Alternative image URL |
| `{{stickerUrl}}` | Telegram sticker URL |
| `{{time}}` | Despawn/hatch/end time |
| `{{date}}` | Date |
| `{{tthm}}` | Minutes until despawn |
| `{{tths}}` | Seconds until despawn |
| `{{now}}` | Current time |
| `{{applemap}}` | Apple Maps link |
| `{{googlemap}}` | Google Maps link |
| `{{wazelink}}` | Waze link |
| `{{rdmUrl}}` | RDM map link |
| `{{reactMapUrl}}` | ReactMap link |
| `{{rocketMadUrl}}` | RocketMAD link |

### Pokemon-Specific Variables

| Variable | Description |
|----------|-------------|
| `{{Pokemon id}}` | Pokemon ID number |
| `{{Pokemon form id}}` | Form ID |
| `{{Pokemon form name}}` | Form name |
| `{{iv}}` | IV percentage |
| `{{atk}}` | Attack IV |
| `{{def}}` | Defense IV |
| `{{sta}}` | Stamina IV |
| `{{level}}` | Pokemon level |
| `{{cp}}` | Combat Power |
| `{{weight}}` | Weight |
| `{{height}}` | Height |
| `{{move1}}` | Fast move name |
| `{{move2}}` | Charge move name |
| `{{move1Type}}` | Fast move type |
| `{{move2Type}}` | Charge move type |
| `{{gender}}` | Gender symbol |
| `{{rarity}}` | Rarity group name |
| `{{isShinyPossible}}` | Whether shiny is possible |
| `{{shinyRate}}` | Shiny rate |
| `{{pvpRankGreat}}` | Great League PVP rank |
| `{{pvpRankUltra}}` | Ultra League PVP rank |
| `{{pvpRankLittle}}` | Little League PVP rank |
| `{{pvpCPGreat}}` | Great League CP |
| `{{pvpCPUltra}}` | Ultra League CP |
| `{{pvpCPLittle}}` | Little League CP |

### Raid Variables

| Variable | Description |
|----------|-------------|
| `{{Pokemon name}}` | Raid boss name (empty for eggs) |
| `{{Pokemon id}}` | Raid boss pokemon ID |
| `{{Pokemon level}}` | Raid tier |
| `{{Pokemon move1}}` | Boss fast move |
| `{{Pokemon move2}}` | Boss charge move |
| `{{gym_name}}` | Gym name |
| `{{team}}` | Gym team |
| `{{ex_eligible}}` | EX eligible gym |

### Quest Variables

| Variable | Description |
|----------|-------------|
| `{{quest_task}}` | Quest task description |
| `{{quest_reward}}` | Quest reward description |
| `{{pokestop_name}}` | Pokestop name |
| `{{pokestop_url}}` | Pokestop image URL |

### Invasion Variables

| Variable | Description |
|----------|-------------|
| `{{grunt_type}}` | Grunt type name |
| `{{pokestop_name}}` | Pokestop name |
| `{{Pokemon name}}` | Possible reward pokemon |
| `{{confirmed}}` | Whether lineup is confirmed |

## Discord Embeds

Discord templates use embed format:

```json
{
  "content": "A wild {{Pokemon name}} appeared!",
  "embed": {
    "title": "{{Pokemon name}} {{iv}}% IV",
    "description": "CP: {{cp}} | Level: {{level}}\n{{move1}} / {{move2}}",
    "color": "{{Pokemon color}}",
    "thumbnail": {
      "url": "{{imgUrl}}"
    },
    "image": {
      "url": "{{mapUrl}}"
    },
    "footer": {
      "text": "Despawns at {{time}} ({{tthm}}m {{tths}}s)"
    }
  }
}
```

## Telegram Messages

Telegram templates use HTML formatting:

```json
{
  "message": "<b>{{Pokemon name}}</b> {{iv}}% IV\nCP: {{cp}} | Level: {{level}}\n{{move1}} / {{move2}}\nDespawns at {{time}}",
  "pokemon_sticker": "{{stickerUrl}}"
}
```

## Partials

Handlebars partials allow reusable template fragments. Define them in `config/partials.json`:

```json
{
  "pokemon_stats": "IV: {{iv}}% | CP: {{cp}} | Level: {{level}}",
  "location_info": "{{addr}}\n{{areas}}"
}
```

Use partials in templates: `{{> pokemon_stats}}`

For advanced template features, see [Advanced DTS](dtsadvanced.md).
