# Alert Templates (DTS)

DTS (Dynamic Template System) controls how alert messages are formatted for Discord and Telegram. Templates use [Handlebars](https://handlebarsjs.com/) syntax.

## Template Files

Templates are defined in `config/dts.json`. If this file doesn't exist, the bundled default from `fallbacks/dts.json` is used.

Additional DTS files can be placed in `config/dts/` — they are merged with `dts.json`.

To customize templates, copy from `examples/` to `config/`:

```sh
cp examples/dts.json config/dts.json
```

The `examples/dts/` directory contains community-contributed templates for inspiration.

## Structure

The DTS file is a JSON array of template objects. Each object specifies the alert type, platform, language, and template content:

```json
[
  {
    "id": 1,
    "language": "en",
    "type": "monster",
    "default": true,
    "platform": "discord",
    "template": {
      "embed": { ... }
    }
  },
  {
    "id": 1,
    "language": "en",
    "type": "monster",
    "default": true,
    "platform": "telegram",
    "template": {
      "content": "...",
      "sticker": "{{{stickerUrl}}}",
      "parse_mode": "Markdown",
      "location": true,
      "webpage_preview": true
    }
  }
]
```

### Template IDs

Each alert type can have multiple numbered templates (e.g. `"id": 1`, `"id": 2`). Users select which template to use when setting up tracking:

```
!track pikachu template2
```

The default template is controlled by `[general] default_template_name`.

### Template Matching

Templates are matched by `type`, `platform`, and `language`. If no exact language match is found, the default template is used.

## Alert Types

| Type | Trigger |
|------|---------|
| `monster` | Wild pokemon with IV data |
| `monsterNoIv` | Wild pokemon without IV data |
| `raid` | Active raid boss |
| `egg` | Raid egg (boss unknown) |
| `quest` | Field research quest |
| `invasion` | Team Rocket invasion |
| `lure` | Lure module on pokestop |
| `nest` | Nest pokemon change |
| `weatherchange` | Weather change |
| `gym` | Gym team change |
| `fort-update` | Fort (gym/pokestop) metadata change |
| `greeting` | Welcome message for new users |

## Common Variables

These variables are available across most alert types:

### Location & Maps

| Variable | Description |
|----------|-------------|
| `{{{addr}}}` | Geocoded address (use triple braces for unescaped HTML) |
| `{{city}}` | City name |
| `{{zipcode}}` | Zip/postal code |
| `{{country}}` | Country |
| `{{state}}` | State/province |
| `{{neighbourhood}}` | Neighbourhood |
| `{{streetName}}` | Street name |
| `{{streetNumber}}` | Street number |
| `{{latitude}}` | Latitude |
| `{{longitude}}` | Longitude |
| `{{areas}}` | Comma-separated matching geofence names |
| `{{{staticMap}}}` | Static map image URL |
| `{{{googleMapUrl}}}` | Google Maps link |
| `{{{appleMapUrl}}}` | Apple Maps link |
| `{{{wazeMapUrl}}}` | Waze navigation link |
| `{{{rdmUrl}}}` | RDM map link (if configured) |
| `{{{reactMapUrl}}}` | ReactMap link (if configured) |
| `{{{rocketMadUrl}}}` | RocketMAD link (if configured) |

### Images

| Variable | Description |
|----------|-------------|
| `{{{imgUrl}}}` | Primary image URL (pokemon/item/gym) |
| `{{{imgUrlAlt}}}` | Alternative image URL |
| `{{{stickerUrl}}}` | Telegram sticker URL |

### Time

| Variable | Description |
|----------|-------------|
| `{{time}}` | Formatted despawn/end time |
| `{{tthh}}` | Hours remaining |
| `{{tthm}}` | Minutes remaining |
| `{{tths}}` | Seconds remaining |
| `{{now}}` | Current time |
| `{{nowISO}}` | Current time in ISO format |
| `{{disappear_time}}` | Unix timestamp (for Discord `<t:{{disappear_time}}:R>`) |

### Other

| Variable | Description |
|----------|-------------|
| `{{prefix}}` | Bot command prefix (e.g. `!`) |
| `{{distance}}` | Distance from user's location |
| `{{bearing}}` | Bearing from user's location |
| `{{bearingEmoji}}` | Cardinal direction emoji |

## Monster Variables

Available in `monster` and `monsterNoIv` templates:

### Pokemon Info

| Variable | Description |
|----------|-------------|
| `{{name}}` | Pokemon name (translated) |
| `{{nameEng}}` | Pokemon name (English) |
| `{{formName}}` | Form name (translated) |
| `{{formNameEng}}` | Form name (English) |
| `{{fullName}}` | Name + form (translated), e.g. "Pikachu Libre" |
| `{{fullNameEng}}` | Name + form (English) |
| `{{id}}` | Pokedex number |
| `{{formId}}` | Form ID |
| `{{generation}}` | Generation number |
| `{{generationName}}` | Generation name (translated) |
| `{{generationRoman}}` | Generation in Roman numerals |
| `{{color}}` | Pokemon type color (hex) |

### Stats (IV Pokemon Only)

| Variable | Description |
|----------|-------------|
| `{{iv}}` | IV percentage (use `{{round iv}}` to round) |
| `{{atk}}` | Attack IV (0-15) |
| `{{def}}` | Defense IV (0-15) |
| `{{sta}}` | Stamina IV (0-15) |
| `{{cp}}` | Combat Power |
| `{{level}}` | Pokemon level |
| `{{weight}}` | Weight |
| `{{height}}` | Height |
| `{{ivColor}}` | Hex color based on IV tier |

### Moves

| Variable | Description |
|----------|-------------|
| `{{quickMoveName}}` | Fast move name (translated) |
| `{{chargeMoveName}}` | Charge move name (translated) |
| `{{quickMoveEmoji}}` | Fast move type emoji |
| `{{chargeMoveEmoji}}` | Charge move type emoji |
| `{{quickMoveId}}` | Fast move ID |
| `{{chargeMoveId}}` | Charge move ID |

### Type & Appearance

| Variable | Description |
|----------|-------------|
| `{{emojiString}}` | Type emoji(s) combined |
| `{{typeEmoji}}` | Same as emojiString |
| `{{typeName}}` | Type name(s) comma-separated |
| `{{genderData.emoji}}` | Gender emoji |
| `{{genderData.name}}` | Gender name |
| `{{size}}` | Size category |
| `{{sizeName}}` | Size name (translated) |
| `{{rarityName}}` | Rarity name (translated) |
| `{{boosted}}` | Boolean — is weather boosted |
| `{{boostWeatherEmoji}}` | Weather boost emoji (empty if not boosted) |
| `{{boostWeatherName}}` | Boost weather name |
| `{{gameWeatherName}}` | Current cell weather name |
| `{{gameWeatherEmoji}}` | Current cell weather emoji |
| `{{shinyPossible}}` | Boolean — can be shiny |
| `{{shinyPossibleEmoji}}` | Shiny sparkle emoji (if possible) |
| `{{shinyStats}}` | Shiny rate (if known) |

### PVP Rankings

| Variable | Description |
|----------|-------------|
| `{{pvpAvailable}}` | Boolean — any PVP data available |
| `{{bestGreatLeagueRank}}` | Best Great League rank |
| `{{bestUltraLeagueRank}}` | Best Ultra League rank |
| `{{bestLittleLeagueRank}}` | Best Little League rank |
| `{{bestGreatLeagueRankCP}}` | CP at best Great League rank |
| `{{bestUltraLeagueRankCP}}` | CP at best Ultra League rank |
| `{{pvpGreat}}` | Array of Great League rankings (see below) |
| `{{pvpUltra}}` | Array of Ultra League rankings |
| `{{pvpLittle}}` | Array of Little League rankings |
| `{{pvpGreatBest}}` | Best Great League entry |
| `{{pvpUltraBest}}` | Best Ultra League entry |
| `{{pvpLittleBest}}` | Best Little League entry |
| `{{userHasPvpTracks}}` | Boolean — user has PVP tracking |

Each PVP ranking entry (used with `{{#each pvpGreat}}`) has:

- `{{rank}}` — PVP rank
- `{{fullName}}` — Pokemon name (may be an evolution)
- `{{cp}}` — CP at league cap
- `{{level}}` — Level needed
- `{{levelWithCap}}` — Level with cap notation
- `{{percentage}}` — Stat product percentage

### Weather Forecast

| Variable | Description |
|----------|-------------|
| `{{weatherChange}}` | Formatted weather change notice |
| `{{weatherCurrentName}}` | Current weather name |
| `{{weatherCurrentEmoji}}` | Current weather emoji |
| `{{weatherNextName}}` | Forecasted weather name |
| `{{weatherNextEmoji}}` | Forecasted weather emoji |
| `{{weatherChangeTime}}` | Time of weather change |

### Events

| Variable | Description |
|----------|-------------|
| `{{futureEvent}}` | Boolean — upcoming event affects this pokemon |
| `{{futureEventName}}` | Event name |
| `{{futureEventTime}}` | Event start/end time |
| `{{futureEventTrigger}}` | `'start'` or `'end'` |

### Other Monster Fields

| Variable | Description |
|----------|-------------|
| `{{disguisePokemonName}}` | Ditto disguise pokemon name |
| `{{seenType}}` | How seen: `encounter`, `wild`, `pokestop`, `cell`, `lure` |
| `{{confirmedTime}}` | Whether despawn time is verified |
| `{{catchBase}}` | Base catch rate % |
| `{{catchGreat}}` | Great ball catch rate % |
| `{{catchUltra}}` | Ultra ball catch rate % |
| `{{baseStats}}` | Object with `baseAttack`, `baseDefense`, `baseStamina` |

## Raid & Egg Variables

| Variable | Description |
|----------|-------------|
| `{{fullName}}` | Raid boss name with form (empty for eggs) |
| `{{name}}` | Raid boss name (translated) |
| `{{levelName}}` | Raid level text (e.g. "Level 5") |
| `{{level}}` | Numeric raid level |
| `{{cp}}` | Boss CP |
| `{{{gymName}}}` | Gym name (use triple braces) |
| `{{{gymUrl}}}` | Gym image URL |
| `{{gymColor}}` | Gym team color (hex) |
| `{{teamName}}` | Gym team name |
| `{{ex}}` | Boolean — EX-eligible gym |
| `{{quickMoveName}}` | Boss fast move (translated) |
| `{{chargeMoveName}}` | Boss charge move (translated) |
| `{{quickMoveEmoji}}` | Fast move type emoji |
| `{{chargeMoveEmoji}}` | Charge move type emoji |
| `{{boosted}}` | Boolean — weather boosted |
| `{{evolution}}` | Evolution type (mega) |

## Quest Variables

| Variable | Description |
|----------|-------------|
| `{{{pokestopName}}}` | Pokestop name |
| `{{{questString}}}` | Quest task description |
| `{{{rewardString}}}` | Reward description |
| `{{with_ar}}` | Boolean — AR scan required |
| `{{{pokestopUrl}}}` | Pokestop image URL |

## Invasion Variables

| Variable | Description |
|----------|-------------|
| `{{{pokestopName}}}` | Pokestop name |
| `{{gruntType}}` | Grunt type name (e.g. "Dragon") |
| `{{gruntTypeColor}}` | Type color (hex) |
| `{{gruntTypeEmoji}}` | Type emoji |
| `{{genderData.name}}` | Grunt gender name |
| `{{genderData.emoji}}` | Grunt gender emoji |
| `{{gruntRewardsList}}` | Reward tiers (see example below) |

The `gruntRewardsList` has `.first` and `.second` tiers, each with `chance` (percentage) and `monsters` (array with `.name`).

## Lure Variables

| Variable | Description |
|----------|-------------|
| `{{{pokestopName}}}` | Pokestop name |
| `{{lureTypeName}}` | Lure type (e.g. "Glacial", "Mossy") |
| `{{lureTypeId}}` | Lure type ID |
| `{{lureTypeColor}}` | Lure type color (hex) |
| `{{lureTypeEmoji}}` | Lure type emoji |

## Nest Variables

| Variable | Description |
|----------|-------------|
| `{{name}}` | Pokemon name (translated) |
| `{{nestName}}` | Nest location name |
| `{{pokemonSpawnAvg}}` | Average spawns per hour |
| `{{resetDate}}` | Nest cycle start date |
| `{{color}}` | Pokemon type color |
| `{{emojiString}}` | Type emoji(s) |

## Weather Change Variables

| Variable | Description |
|----------|-------------|
| `{{weatherName}}` | New weather name |
| `{{{weatherEmoji}}}` | New weather emoji |
| `{{oldWeatherName}}` | Previous weather name |
| `{{{oldWeatherEmoji}}}` | Previous weather emoji |
| `{{activePokemons}}` | Array of affected pokemon |

Each entry in `activePokemons` has `.name`, `.formName`, `.iv`, `.cp`.

## Gym Variables

| Variable | Description |
|----------|-------------|
| `{{{gymName}}}` | Gym name |
| `{{teamName}}` | New controlling team |
| `{{previousControlName}}` | Previous team name |
| `{{slotsAvailable}}` | Available slots (0-6) |
| `{{trainerCount}}` | Number of defenders |
| `{{color}}` | Team color (hex) |
| `{{{gymUrl}}}` | Gym image URL |
| `{{inBattle}}` | Boolean — gym under attack |

## Fort Update Variables

| Variable | Description |
|----------|-------------|
| `{{changeTypeText}}` | Change type: "New", "Edit", "Removal" |
| `{{fortTypeText}}` | Fort type: "Gym" or "Pokestop" |
| `{{name}}` | Fort name |
| `{{description}}` | Fort description |
| `{{oldName}}` | Previous name (on edit) |
| `{{newName}}` | New name (on edit) |
| `{{oldDescription}}` | Previous description |
| `{{newDescription}}` | New description |
| `{{isEditName}}` | Boolean — name was changed |
| `{{isEditDescription}}` | Boolean — description was changed |

## Discord Template Format

Discord templates use an `embed` object:

```json
{
  "embed": {
    "color": "{{ivColor}}",
    "title": "{{round iv}}% {{fullName}} cp:{{cp}} L:{{level}} {{atk}}/{{def}}/{{sta}} {{{boostWeatherEmoji}}}",
    "description": "End: {{time}}, Time left: {{tthm}}m {{tths}}s\n{{{addr}}}\nquick: {{quickMoveName}}, charge: {{chargeMoveName}}\nMaps: [Google]({{{googleMapUrl}}}) | [Apple]({{{appleMapUrl}}})",
    "thumbnail": {
      "url": "{{{imgUrl}}}"
    },
    "image": {
      "url": "{{{staticMap}}}"
    }
  }
}
```

!!! note "Triple Braces"
    Use `{{{variable}}}` (triple braces) for URLs and content that may contain special characters. Double braces `{{variable}}` HTML-escape the output.

## Telegram Template Format

Telegram templates use a `content` string with optional `sticker`, `parse_mode`, `location`, and `webpage_preview` fields:

```json
{
  "content": "{{round iv}}% {{fullName}} cp:{{cp}} L:{{level}}\n{{{addr}}}\nMaps: [Google]({{{googleMapUrl}}}) | [Apple]({{{appleMapUrl}}})",
  "sticker": "{{{stickerUrl}}}",
  "parse_mode": "Markdown",
  "location": true,
  "webpage_preview": true
}
```

## Real-World Examples

### Monster Alert with PVP Rankings (from default template)

```json
{
  "embed": {
    "color": "{{ivColor}}",
    "title": "{{round iv}}% {{fullName}} cp:{{cp}} L:{{level}} {{atk}}/{{def}}/{{sta}} {{{boostWeatherEmoji}}}",
    "description": "End: {{time}}, Time left: {{tthm}}m {{tths}}s \n {{#if weatherChange}}{{{weatherChange}}}\n{{/if}}{{#if futureEvent}}There is an event ({{futureEventName}}) {{#eq futureEventTrigger 'start'}}starting{{else}}ending{{/eq}} at {{futureEventTime}}.\n{{/if}}{{{addr}}} \n quick: {{quickMoveName}}, charge: {{chargeMoveName}} \n{{#if pvpGreat}}**Great league:**\n{{#each pvpGreat}} - {{fullName}} #{{rank}} @{{cp}}CP (Lvl. {{levelWithCap}})\n{{/each}}{{/if}}{{#if pvpUltra}}**Ultra league:**\n{{#each pvpUltra}} - {{fullName}} #{{rank}} @{{cp}}CP (Lvl. {{levelWithCap}})\n{{/each}}{{/if}} Maps: [Google]({{{googleMapUrl}}}) | [Apple]({{{appleMapUrl}}})",
    "thumbnail": {
      "url": "{{{imgUrl}}}"
    },
    "image": {
      "url": "{{{staticMap}}}"
    }
  }
}
```

### Invasion with Conditional Reward Display

```json
{
  "embed": {
    "title": "Team Rocket at {{{pokestopName}}}",
    "color": "{{gruntTypeColor}}",
    "description": "Type: {{gruntType}} {{gruntTypeEmoji}}\nGender: {{genderData.name}}{{genderData.emoji}}\nPossible rewards: {{#compare gruntRewardsList.first.chance '==' 100}}{{#forEach gruntRewardsList.first.monsters}}{{this.name}}{{#unless isLast}}, {{/unless}}{{/forEach}}{{/compare}}{{#compare gruntRewardsList.first.chance '<' 100}}\n ‣ {{gruntRewardsList.first.chance}}% : {{#forEach gruntRewardsList.first.monsters}}{{this.name}}{{#unless isLast}}, {{/unless}}{{/forEach}}\n ‣ {{gruntRewardsList.second.chance}}% : {{#forEach gruntRewardsList.second.monsters}}{{this.name}}{{#unless isLast}}, {{/unless}}{{/forEach}}{{/compare}}\n Ends: {{time}}, in ({{#if tthh}}{{tthh}}h {{/if}}{{tthm}}m {{tths}}s)"
  }
}
```

### No-IV Pokemon (MonsterNoIv)

```json
{
  "embed": {
    "color": "{{color}}",
    "title": "?% {{fullName}} {{{boostWeatherEmoji}}}",
    "description": "Ends: {{time}}, Time left: {{tthm}}m {{tths}}s \n {{{addr}}} \n Maps: [Google]({{{googleMapUrl}}}) | [Apple]({{{appleMapUrl}}})"
  }
}
```

### Weather Change with Affected Pokemon

```json
{
  "embed": {
    "title": "⚠️ Weather change ⚠️",
    "description": "{{#if oldWeatherName}}It went from {{oldWeatherName}} {{{oldWeatherEmoji}}} to {{else}}It is now {{/if}}{{weatherName}} {{{weatherEmoji}}}\n{{#if activePokemons}}The following Pokémon have changed:\n{{#each activePokemons}}**{{this.name}}** {{#isnt this.formName 'Normal'}} {{this.formName}}{{/isnt}} - {{round this.iv}}% - {{this.cp}}CP\n{{/each}}{{else}}This could have altered reported stats and IV...{{/if}}"
  }
}
```

## Partials

Partials are reusable template fragments defined in `config/partials.json`. Include them with `{{> partialName}}`.

Example partial definition:

```json
{
  "remainingTime": "{{#if tthh}}{{tthh}}h {{/if}}{{tthm}}m {{tths}}s"
}
```

Use in a template:

```
Time left: {{> remainingTime}}
```

## Template Testing

Test your templates with the `!poracle-test` command:

```
!poracle-test pokemon
!poracle-test raid
!poracle-test pokestop
!poracle-test gym
!poracle-test nest
!poracle-test quest
!poracle-test fort-update
!poracle-test max-battle
```

This sends a test alert using sample data from `config/testdata.json` (or the bundled fallback).

For advanced template features (helpers, calculations, custom maps), see [Advanced DTS](dtsadvanced.md).
