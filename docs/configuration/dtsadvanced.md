# Advanced DTS

Advanced features for alert templates. For basics, see [Alert Templates (DTS)](dts.md).

## Handlebars Helpers

PoracleNG registers custom Handlebars helpers plus the full [@budibase/handlebars-helpers](https://github.com/nicedoc/handlebars-helpers) library, which provides a wide range of comparison, string, math, and collection helpers.

### Conditionals

```handlebars
{{!-- Simple if/else --}}
{{#if iv}}IV: {{round iv}}%{{else}}No IV data{{/if}}

{{!-- Check equality --}}
{{#eq futureEventTrigger 'start'}}starting{{else}}ending{{/eq}}

{{!-- Check inequality --}}
{{#isnt formName 'Normal'}} {{formName}}{{/isnt}}

{{!-- Numeric comparison --}}
{{#compare gruntRewardsList.first.chance '==' 100}}
  Guaranteed: {{#forEach gruntRewardsList.first.monsters}}{{this.name}}{{/forEach}}
{{/compare}}

{{#compare gruntRewardsList.first.chance '<' 100}}
  {{gruntRewardsList.first.chance}}% chance
{{/compare}}

{{!-- Greater/less than --}}
{{#gt tthm 30}}More than 30 minutes left{{/gt}}
{{#lte level 5}}Low level pokemon{{/lte}}

{{!-- Boolean logic --}}
{{#and pvpAvailable userHasPvpTracks}}Show PVP info{{/and}}
{{#or pvpGreat pvpUltra}}Has PVP data{{/or}}

{{!-- Unless (negation) --}}
{{#unless confirmedTime}}⚠️ Unverified despawn time{{/unless}}
```

### Iterating Arrays

```handlebars
{{!-- Loop over PVP rankings --}}
{{#each pvpGreat}}
  - {{fullName}} #{{rank}} @{{cp}}CP (Lvl. {{levelWithCap}})
{{/each}}

{{!-- forEach with isLast check --}}
{{#forEach gruntRewardsList.first.monsters}}
  {{this.name}}{{#unless isLast}}, {{/unless}}
{{/forEach}}

{{!-- Loop over weather-affected pokemon --}}
{{#each activePokemons}}
  **{{this.name}}** {{#isnt this.formName 'Normal'}}{{this.formName}}{{/isnt}} - {{round this.iv}}% - {{this.cp}}CP
{{/each}}

{{!-- Access parent scope from inside loop --}}
{{#each pvpGreat}}
  {{fullName}} - {{../name}} base pokemon
{{/each}}
```

### Number Formatting

```handlebars
{{!-- Round to integer --}}
{{round iv}}

{{!-- Format with specific decimal places --}}
{{numberFormat iv 1}}

{{!-- Pad with zeros (default 3 digits) --}}
{{pad0 id}}          {{!-- 25 → "025" --}}
{{pad0 id 4}}        {{!-- 25 → "0025" --}}

{{!-- Arithmetic --}}
{{minus lureTypeId 500}}    {{!-- Subtract --}}
{{sum level 1}}             {{!-- Add --}}

{{!-- Thousands separator --}}
{{addCommas stardust}}      {{!-- 125000 → "125,000" --}}
```

### String Helpers

```handlebars
{{!-- Concatenation --}}
{{concat 'Level ' level ' raid'}}

{{!-- Lowercase --}}
{{lowercase name}}
```

## Pokemon Lookup Helpers

These helpers look up game data by ID, translating to the user's language:

### Move Helpers

```handlebars
{{!-- Look up move by ID --}}
{{moveName quickMoveId}}          {{!-- Translated move name --}}
{{moveNameEng quickMoveId}}       {{!-- English move name --}}
{{moveNameAlt quickMoveId}}       {{!-- Alt language move name --}}
{{moveType quickMoveId}}          {{!-- Move type (translated) --}}
{{moveTypeEng quickMoveId}}       {{!-- Move type (English) --}}
{{moveEmoji quickMoveId}}         {{!-- Move type emoji --}}
```

### Pokemon Name Helpers

```handlebars
{{!-- Look up pokemon by ID --}}
{{pokemonName 25}}                {{!-- "Pikachu" (translated) --}}
{{pokemonNameEng 25}}             {{!-- "Pikachu" (English) --}}
{{pokemonNameAlt 25}}             {{!-- Alt language name --}}
{{pokemonForm formId}}            {{!-- Form name (translated) --}}
{{pokemonFormEng formId}}         {{!-- Form name (English) --}}

{{!-- Alt translation helper --}}
{{translateAlt name}}             {{!-- Name in secondary language --}}
```

### Pokemon Block Helper

The `{{#pokemon}}` block helper provides detailed info about a pokemon by ID and form:

```handlebars
{{#pokemon id formId}}
  Name: {{name}} ({{nameEng}})
  Full: {{fullName}}
  Type: {{typeName}} {{typeEmoji}}
  Has evolutions: {{hasEvolutions}}
  Base stats: {{baseStats.baseAttack}}/{{baseStats.baseDefense}}/{{baseStats.baseStamina}}
{{/pokemon}}
```

Available fields inside the block: `name`, `nameEng`, `formName`, `formNameEng`, `fullName`, `fullNameEng`, `formNormalised`, `formNormalisedEng`, `emoji`, `typeNameEng`, `typeName`, `typeEmoji`, `hasEvolutions`, `baseStats`.

### CP Calculation

Calculate what CP a pokemon would be at a given level and IVs:

```handlebars
{{calculateCp baseStats level atk def sta}}
```

Example:

```handlebars
{{calculateCp baseStats 40 15 15 15}}
```

### Power-Up Cost

Calculate stardust and candy cost to power up between levels:

```handlebars
{{!-- As a string --}}
{{getPowerUpCost level 40}}

{{!-- As a block with individual fields --}}
{{#getPowerUpCost level 40}}
  Cost: {{stardust}} Stardust, {{candy}} Candy{{#if xlCandy}}, {{xlCandy}} XL Candy{{/if}}
{{/getPowerUpCost}}
```

### Base Stats Lookup

```handlebars
{{!-- Get base stats for a pokemon --}}
{{#with (pokemonBaseStats id formId)}}
  ATK: {{baseAttack}} DEF: {{baseDefense}} STA: {{baseStamina}}
{{/with}}
```

### Emoji Lookup

```handlebars
{{getEmoji 'shiny'}}
{{getEmoji 'weather_1'}}
```

## Custom Map Lookup

The `map` helper looks up values from custom map files in `config/customMaps/`:

```handlebars
{{map "myMapName" someValue}}

{{!-- As a block --}}
{{#map "ivTier" iv}}
  {{this.label}}: {{this.color}}
{{/map}}
```

There's also `map2` which tries a second key as fallback:

```handlebars
{{#map2 "pokemonNicknames" id formId}}{{this}}{{/map2}}
```

## Multiple Templates

Create multiple template styles for different use cases by using different `id` values:

```json
[
  {
    "id": 1,
    "type": "monster",
    "platform": "discord",
    "template": { "embed": { "title": "Compact: {{round iv}}% {{name}}" } }
  },
  {
    "id": 2,
    "type": "monster",
    "platform": "discord",
    "template": { "embed": { "title": "Detailed: {{fullName}}", "description": "Full stats..." } }
  }
]
```

Users select templates with the `template` flag:

```
!track pikachu template2
```

## Multi-Language Templates

Templates can be language-specific. Add the `language` field to target a locale:

```json
{
  "id": 1,
  "language": "de",
  "type": "monster",
  "platform": "discord",
  "template": { ... }
}
```

If no template matches the user's language, the default (no language or `"en"`) is used.

## Custom Emoji

Define custom emoji mappings in `config/emoji.json`:

```json
{
  "shiny": "<:shiny:123456789>",
  "weather_1": "<:sunny:123456789>",
  "pokemon_type_fire": "<:fire:123456789>"
}
```

Reference in templates with `{{getEmoji 'shiny'}}`.

## Broadcast Templates

Define reusable broadcast message templates in `config/broadcast.json` for admin broadcasts.

## Channel Templates

Auto-create Discord channels from templates in `config/channelTemplate.json`.

## Telegram-Specific Options

Telegram templates support these extra fields:

| Field | Description |
|-------|-------------|
| `content` | Message text (can be string or array of strings) |
| `sticker` | Sticker image URL |
| `parse_mode` | `"Markdown"` or `"HTML"` |
| `location` | `true` to include a location pin |
| `webpage_preview` | `true` to show link previews |

## Tips

- Always test templates after changes with `!poracle-test`
- Use `{{{triple braces}}}` for URLs and any content with special characters
- Use `{{round iv}}` instead of raw `{{iv}}` to avoid long decimal numbers
- Use `{{#if field}}` to handle optional fields that may be empty
- The `{{#isnt formName 'Normal'}}` pattern hides "Normal" form names
- Discord embeds have character limits: title (256), description (4096), footer (2048)
- The `examples/dts/` directory contains community-contributed templates for inspiration
- Use `{{> partialName}}` to reuse common template fragments across alert types
