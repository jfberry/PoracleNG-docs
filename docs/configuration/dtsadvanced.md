# Advanced DTS

Helpers, custom maps, emoji, multi-language templates and interactive buttons. For the basics, see [Alert Templates (DTS)](dts.md). For the fields each alert type provides, see the [DTS Field Reference](dtsfields.md).

## Handlebars Helpers

PoracleNG uses a **fixed set** of helpers, listed in the [Helper Reference](#helper-reference) below. It does **not** include the `@budibase/handlebars-helpers` library that PoracleJS used. A helper that isn't in the list prints nothing and shows no error, so a template copied from PoracleJS can lose text without any warning. **5.3+** adds `capitalizeAll`, `ellipsis`, `startsWith` and `remove` to cover the PoracleJS helpers templates use most.

### Conditionals

Every comparison helper can be used in three ways:

- as a block: `{{#eq a b}}…{{else}}…{{/eq}}`
- as a subexpression: `{{#if (eq a b)}}`
- inline: `{{eq a b}}` prints `true` or `false`

```handlebars
{{#if iv}}IV: {{round iv}}%{{else}}No IV data{{/if}}

{{#eq futureEventTrigger 'start'}}starting{{else}}ending{{/eq}}

{{#isnt formName 'Normal'}} {{formName}}{{/isnt}}

{{#compare gruntRewardsList.first.chance '<' 100}}{{gruntRewardsList.first.chance}}% chance{{/compare}}

{{#gt tthm 30}}More than 30 minutes left{{/gt}}

{{#and pvpAvailable userHasPvpTracks}}Show PVP info{{/and}}
{{#or pvpGreat pvpUltra}}Has PVP data{{/or}}

{{!-- "value is one of" — {{#or size 1 5}} would only test that some argument is truthy --}}
{{#oneOf size 1 5}}Tiny or huge!{{/oneOf}}

{{#if (and (gte iv 90) (lt cp 1500))}}Great league candidate{{/if}}

{{#unless confirmedTime}}⚠️ Unverified despawn time{{/unless}}
```

`eq`, `ne`, `is`, `isnt` and `oneOf` compare values as numbers when both sides look like numbers (so `"100.00"` equals `100`), and as text otherwise. `compare` with `==`/`!=` always compares text. `gt`, `gte`, `lt` and `lte` always compare numbers.

### Iterating

```handlebars
{{#each pvpGreat}}
  - {{fullName}} #{{rank}} @{{cp}}CP (Lvl. {{levelWithCap}}){{#unless isLast}},{{/unless}}
{{/each}}

{{#forEach gruntRewardsList.first.monsters}}{{this.name}}{{#unless isLast}}, {{/unless}}{{/forEach}}

{{!-- reach the alert's own fields from inside a loop --}}
{{#each evolutions}}{{fullName}} ({{../name}}){{/each}}
```

Inside `each` and `forEach` you can use the PoracleJS-style `isFirst`/`isLast` fields as well as `@index`, `@first`, `@last` and `@key`. `forEach` also gives `@total`. Both show their `{{else}}` block when the list is empty.

### Helper Reference

**Comparison and logic**

| Helper | Usage |
|---|---|
| `eq` / `is` | `{{#eq a b}}` equal |
| `ne` / `isnt` | `{{#ne a b}}` not equal |
| `gt`, `gte`, `lt`, `lte` | Numeric comparisons |
| `compare` | `{{#compare a '<=' b}}`, with operators `==`, `!=`, `<`, `>`, `<=`, `>=` |
| `and`, `or` | Any number of arguments: all true / any true |
| `neither` | Any number of arguments: none true |
| `oneOf` | `{{#oneOf value a b c}}`: value equals one of the others |
| `not` | `{{#not x}}…{{else}}…{{/not}}` (block only) |
| `contains` | `{{#contains collection value}}`: text contains a substring, or a list contains an item (block only) |
| `startsWith` (**5.3+**) | `{{#startsWith "EX" gymName}}`: the prefix comes **first**. Also works as a subexpression. |
| `default` | `{{default value "fallback"}}`: the value if it's set, otherwise the fallback |

**Math** (all return numbers)

| Helper | Usage |
|---|---|
| `add` / `plus` | `{{add a b}}` |
| `subtract` / `minus` | `{{minus lureTypeId 500}}` |
| `multiply`, `divide` | Dividing by 0 gives `0` |
| `round`, `floor`, `ceil` | `{{round iv}}` |
| `toFixed` / `numberFormat` | `{{toFixed iv 1}}`: fixed number of decimals |
| `toInt` | Drops the decimal part |
| `addCommas` | `{{addCommas 125000}}` gives `125,000` |
| `pad0` | `{{pad0 id}}` gives `025`, `{{pad0 id 4}}` gives `0025` |

There is no `sum` helper. Use `add`.

**Text**

| Helper | Usage |
|---|---|
| `uppercase`, `lowercase` | Change case |
| `capitalize` | Capitalise the first letter |
| `capitalizeAll` (**5.3+**) | Capitalise the first letter of every word: `(old) church` gives `(Old) Church` |
| `concat` | `{{concat 'Level ' level ' raid'}}` (any number of arguments) |
| `replace`, `replaceFirst` | `{{replace s "old" "new"}}` replaces every match; `replaceFirst` only the first |
| `remove` (**5.3+**) | `{{remove gymName " (EX)"}}` removes every match |
| `truncate` | `{{truncate s 20}}` or `{{truncate s 20 suffix="…"}}`. The suffix counts towards the length and defaults to `...`. Counts bytes. |
| `ellipsis` (**5.3+**) | `{{ellipsis gymName 20}}`: the first 20 characters, plus `…` if the text was longer |
| `escape` | HTML-escape a value |

**Lists**

| Helper | Usage |
|---|---|
| `each`, `forEach` | Loops (see above) |
| `length` | Length of a list, map or text |
| `join` | `{{join typeNameEng ", "}}` |
| `first`, `last` | `{{first list}}`, or `{{first list n=2}}` for the first two |

**Game data** (these use the template's language and platform)

| Helper | Usage |
|---|---|
| `pokemonName`, `pokemonNameEng`, `pokemonNameAlt` | `{{pokemonName 25}}` |
| `pokemonForm`, `pokemonFormEng`, `pokemonFormAlt` | `{{pokemonForm formId}}` |
| `pokemon` | Block helper, see [below](#pokemon-block-helper) |
| `pokemonBaseStats` | `{{#with (pokemonBaseStats id formId)}}{{baseAttack}}{{/with}}` |
| `calculateCp` | `{{calculateCp baseStats level atk def sta}}` or `{{calculateCp pokemonId formId level atk def sta}}` |
| `getPowerUpCost` | `{{getPowerUpCost level 40}}` or as a block with `stardust`, `candy` and `xlCandy` |
| `moveName`, `moveNameEng`, `moveNameAlt` | `{{moveName quickMoveId}}` |
| `moveType`, `moveTypeEng`, `moveTypeAlt` | Move type name |
| `moveEmoji`, `moveEmojiEng`, `moveEmojiAlt` | Move type emoji (all three give the same result) |
| `getEmoji` | `{{getEmoji 'shiny'}}`, see [Custom Emoji](#custom-emoji) |
| `translateAlt` | Translate a key into the alt language |
| `map`, `map2` | See [Custom Maps](#custom-maps) |

!!! warning "Argument counts matter"
    Calling a helper with too few arguments (e.g. `{{add 1}}`) makes the whole template fail. The user gets a fallback message instead of the alert (`Template render error: …` on 5.3+, `Template not found: …` on 5.2) and the admin channel is notified.

### Template Variables (`@language`, `@platform`, `@altLanguage`)

Each render sets three variables that templates can read:

| Variable | Value |
|---|---|
| `@language` | The recipient's language (or `[general] locale`) |
| `@platform` | `discord` or `telegram` |
| `@altLanguage` | `[locale] language` (default `en`); used by the `*Alt` helpers |

```handlebars
{{#eq @platform 'telegram'}}*bold*{{else}}**bold**{{/eq}}
{{#ne @language 'en'}}({{nameEng}}){{/ne}}
```

On 5.3+ `help` and `greeting` templates get `@language` and `@platform` too: `!help` and the `!poracle` greeting use the user's language, and greetings sent by role reconciliation use `[general] locale`. On 5.2 these templates have no variables, so their helpers always use English.

## Pokemon Lookup Helpers

### Pokemon Block Helper

```handlebars
{{#pokemon id formId}}
  {{fullName}} ({{fullNameEng}}): {{join typeName "/"}} {{join typeEmoji ""}}
  Base stats: {{baseStats.baseAttack}}/{{baseStats.baseDefense}}/{{baseStats.baseStamina}}
{{/pokemon}}
```

Inside the block you can use `name`, `nameEng`, `formName`, `formNameEng`, `fullName`, `fullNameEng`, `typeName`, `typeNameEng`, `typeEmoji`, `baseStats` and `hasEvolutions`. Note that:

- `typeName`, `typeNameEng` and `typeEmoji` are **lists** here (unlike the alert fields), so use `join`.
- `fullName` puts the form in brackets, e.g. `Pikachu (Libre)`, and leaves out Normal forms.
- The form is optional on 5.3+: `{{#pokemon id}}` uses the base form. On 5.2 both arguments are required (pass `0` as the form), and `{{#pokemon id}}` makes the render fail.
- Used inline, `{{pokemon id formId}}` prints the `fullName`.

### CP and Power-Up Cost

```handlebars
{{calculateCp baseStats 40 15 15 15}}
{{calculateCp 150 0 50 15 15 15}}

{{#getPowerUpCost level 40}}
  Cost: {{stardust}} Stardust, {{candy}} Candy{{#if xlCandy}}, {{xlCandy}} XL Candy{{/if}}
{{/getPowerUpCost}}
```

`baseStats` is available on pokemon, raid, maxbattle, quest (pokemon rewards), PVP entries and evolution entries.

## Custom Maps

Custom maps are lookup tables you define in `config/customMaps/*.json` and read in templates with `{{map}}`. They have nothing to do with static map images. Use them for things like friendly area names, counters for raid bosses, or IV symbols.

!!! note
    The file formats, block mode, `{{else}}` and language variants described below are **5.3+**. On 5.2 each file is a flat `{"key": "value"}` object named after the file, and a missing key prints the key itself.

### File formats

Each file can hold any of these:

```json
{ "name": "raidCounters", "map": { "150": { "trainers": "3", "counters": "Tyranitar, Gengar" } } }
```

```json
[
  { "name": "arealist", "map": { "london": "London (Central)" } },
  { "name": "arealist", "language": "de", "map": { "london": "London (Zentrum)" } }
]
```

```json
{ "london": "London (Central)" }
```

- A map is found by its `name`, not the file name. The flat form (the last example) is named after the file, and `arealist.de.json` is the German version of `arealist`.
- Values can be text, numbers or objects.
- If a version exists for the recipient's language, it's checked first, then the version without a language.
- Maps are read at startup, so restart after changing them. Editors can list them with `GET /api/dts/custommaps`.

`examples/customMaps/` contains `ivSymbol.json` and `raidCounters.json`.

### Using maps

```handlebars
{{!-- Inline: prints the value, or nothing if the key is missing --}}
{{map 'ivSymbol' atk}}{{map 'ivSymbol' def}}{{map 'ivSymbol' sta}}

{{!-- Subexpression: a missing key counts as false --}}
{{#if (map 'raidCounters' pokemon_id)}}Counters available{{/if}}

{{!-- Block: object values become the block's context; {{else}} runs when the key is missing --}}
{{#map 'raidCounters' pokemon_id}}Counters ({{trainers}} trainers): {{counters}}{{else}}No counters yet{{/map}}

{{!-- map2: try a second key if the first is missing --}}
{{map2 'nicknames' (concat pokemon_id '_' form) pokemon_id}}

{{!-- area names --}}
{{#each matched}}{{map 'arealist' this}} {{/each}}
```

Used inline, an object value prints nothing. Use the block form to get at its fields.

## Custom Emoji

Emoji fields (`typeEmoji`, `boostWeatherEmoji`, `genderEmoji`, `teamEmoji`, `lureTypeEmoji`, `bearingEmoji`, `shinyPossibleEmoji` and so on) are looked up by **key**. Each key first checks your per-platform overrides in `config/emoji.json`, then the built-in defaults.

`config/emoji.json` maps **platform → key → emoji**:

```json
{
  "discord": {
    "type-fire": "<:poracle_type_fire:878636941975896085>",
    "weather-sunny": "<:poracle_weather_sunny:878660407739416586>",
    "team-valor": "<:valor:123456789012345678>"
  },
  "telegram": {
    "shiny": "🌟"
  }
}
```

The built-in keys are:

| Group | Keys |
|---|---|
| Types | `type-normal`, `type-fire`, `type-water`, `type-grass`, `type-electric`, `type-ice`, `type-fighting`, `type-poison`, `type-ground`, `type-flying`, `type-psychic`, `type-bug`, `type-rock`, `type-ghost`, `type-dragon`, `type-dark`, `type-steel`, `type-fairy` |
| Weather | `weather-sunny`, `weather-rain`, `weather-partly-cloudy`, `weather-cloudy`, `weather-windy`, `weather-snow`, `weather-fog` |
| Teams | `team-uncontested`, `team-mystic`, `team-valor`, `team-instinct` |
| Lures | `lure-normal`, `lure-glacial`, `lure-mossy`, `lure-magnetic`, `lure-rainy`, `lure-sparkly` |
| Gender | `gender-male`, `gender-female`, `gender-none` |
| Directions | `north`, `northeast`, `east`, `southeast`, `south`, `southwest`, `west`, `northwest` |
| Incidents | `incident-goldstop`, `incident-kecleon`, `incident-showcase` |
| Showcase focus (**5.3+**) | `showcase-focus-pokemon`, `showcase-focus-type`, `showcase-focus-alignment`, `showcase-focus-class`, `showcase-focus-family`, `showcase-focus-buddy`, `showcase-focus-generation`, `showcase-focus-hatched`, `showcase-focus-mega`, `showcase-focus-shiny` (all 🏆 by default; used by `showcaseFocusEmoji`) |
| Other | `shiny`, `grunt-unknown` |

`examples/emoji.json` has a complete Discord set of custom emoji. Use any key directly with `{{getEmoji 'team-valor'}}`. After editing, run `!poracle-admin emoji reload`.

## Multiple Templates

Give entries different `id` values to offer several styles:

```json
[
  { "id": "1", "type": "monster", "platform": "discord", "default": true,
    "template": { "embed": { "title": "Compact: {{round iv}}% {{name}}" } } },
  { "id": "2", "type": "monster", "platform": "discord", "name": "Detailed",
    "template": { "embed": { "title": "Detailed: {{fullName}}", "description": "Full stats..." } } }
]
```

Users choose one with `!track pikachu template:2`.

## Multi-Language Templates

Add a `language` to an entry to target that language. See [Template Selection](dts.md#template-selection) for the full order PoracleNG follows. Keep in mind:

- An entry without a `language` matches every language.
- Ids don't carry over between languages: a user whose language has no entry for their id gets that language's `default` entry.
- If a language has no templates at all, a `default` entry with no language is used if you have one, otherwise the `default` entry for `[general] locale`.
- A `default` entry in the user's language beats a `default` entry with no language. (5.2 had this the wrong way round; see [Template Selection](dts.md#template-selection).)
- The fields themselves (pokemon names, moves, addresses) are always translated into the recipient's language. Use the `*Eng` fields or `*Alt` helpers for a second language.

See [Multi-Language](multilang.md) for setting up languages, and `examples/dts/multilingual/` for examples.

## Broadcast Templates

Define reusable broadcast message templates in `config/broadcast.json` for admin broadcasts.

## Channel Templates

Auto-create Discord channels — and optionally categories, private threads, and a "click to join" picker post — from templates in `config/channelTemplate.json`. See [Channel Auto-create](autocreate.md) for the template format, bulk sync rules, and the `!autocreate` admin command.

## Snapshots

Snapshots are an **opt-in on-disk store of per-delivery enrichment views** — the resolved data that was used to render each alert message. They are required for [Interactive Buttons](#interactive-buttons): button click handlers look up the snapshot of the message you clicked on to know which gym, pokemon, area, etc. you wanted to act on.

Enable in `config.toml`:

```toml
[snapshots]
enabled              = true
# path               = "config/.cache/snapshots"   # relative to BaseDir
# max_age_days       = 7                           # safety-sweep grace period
# sweep_interval_mins = 60                         # background sweep cadence
```

With snapshots disabled (the default), the render path skips Discord `components` entirely — any buttons declared in DTS render as inert no-ops at click time, surfacing as *"This alert has expired."* You can keep your button definitions in templates and flip snapshots on whenever you're ready.

Snapshots are **per-delivery**, not per-event — one snapshot per delivered message, per user, per channel. Edits re-render through the pipeline and overwrite the previous snapshot, so snapshots always reflect "what the user currently sees".

## Interactive Buttons

A template entry may declare a `buttons[]` array attaching one or more Discord interactive buttons to the rendered message. Buttons let users mute one species or gym with a click, unsubscribe from a tracking rule, redeliver an alert to DM, or open a "show me the PVP detail" ephemeral follow-up — all without leaving the alert.

Buttons are operator-authored: they ship as part of the DTS entry, and the operator controls their action, visibility, and styling.

!!! warning "Prerequisite: snapshots"
    Buttons require `[snapshots] enabled = true`. Without snapshots, button click handlers have no resolved view to act against and clicks fail with *"This alert has expired."* See [Snapshots](#snapshots) above.

### Minimal Example (JSON)

```json
{
  "type": "raid",
  "platform": "discord",
  "language": "en",
  "id": "1",
  "template": { "embed": { "title": "{{fullName}} @ {{gymName}}" } },
  "buttons": [
    {
      "id": "mute_gym_1h",
      "label": "Mute this gym (1h)",
      "style": "danger",
      "action": "mute",
      "scope": "gym",
      "params": { "duration_min": 60 }
    }
  ]
}
```

### Minimal Example (TOML)

```toml
[[entry]]
id       = "1"
type     = "raid"
platform = "discord"
language = "en"

template = """
{ "embed": { "title": "{{fullName}} @ {{gymName}}" } }
"""

  [[entry.buttons]]
  id     = "mute_gym_1h"
  label  = "Mute this gym (1h)"
  style  = "danger"
  action = "mute"
  scope  = "gym"
  params = { duration_min = 60 }
```

### Button Fields

| Field | Required | Notes |
|---|---|---|
| `id` | yes | Click-side identifier; encoded in the Discord `custom_id`. Unique per template entry. |
| `label` | yes | Button label shown to the user. |
| `style` | no | `primary` / `secondary` / `success` / `danger`. Defaults to `secondary`. |
| **Dispatch** (pick exactly one) | yes | One of the four shapes below. |
| `action` | one of dispatch | Named handler in the action registry — server-side state change (mute, unsubscribe, redeliver, render). |
| `response_template_id` | one of dispatch | DTS lookup against `type="buttonResponse"`. |
| `response_template_inline` | one of dispatch | Raw Handlebars body; rendered against the snapshot at click time. |
| `response_text` | one of dispatch | Plain-text Handlebars (no JSON parsing). |
| `scope` | for `action="mute"` / `"unsubscribe"` | `gym`, `pokemon`, `area`, `pokestop`, `station`, `everything`, `tracking`. Picks which snapshot field is the identifier. |
| `params` | no | Free-form bag passed verbatim to the handler. `mute` reads `duration_min` (default 60); `render` reads `template_id`. |
| `applies_to` | no | Destination filter — subset of `["dm", "channel", "webhook", "any"]`. Defaults to `["dm"]` for `mute`/`unsubscribe`, `["any"]` otherwise. |
| `show_if` | no | Handlebars expression evaluated at render time; falsy → button is not attached at all. Use for conditional buttons (e.g. PVP-only). |
| `visible_to` | no | Access control: `anyone` (default), `registered` (clicker has a Poracle account), `admin` (clicker is in `[discord] admins`). On DMs the `admin` gate is also enforced at render time. On channels and webhooks the gate is click-time only (Discord doesn't support per-viewer component visibility). |

### Dispatch Modes

1. **`action`** — named handler in the action registry. Writes server-side state (a mute, an unsubscribe, a redeliver). Reply is a short ephemeral confirmation.

2. **`response_template_id`** — looks up an entry where `type="buttonResponse"`. Useful when a response is shared between multiple buttons or alert types.

3. **`response_template_inline`** — render the literal string (or string array, joined by newlines) as a Handlebars template against the snapshot. Same field access as alert templates. JSON-shaped output becomes ephemeral Discord embeds; otherwise it lands as plain content.

4. **`response_text`** — plain-text Handlebars output. No JSON parsing. Use for one-liners like `"📍 \`{{latitude}}, {{longitude}}\`"`.

### Registered Actions

`GET /api/dts/actions` returns the live list. Currently:

| Action | Behaviour |
|---|---|
| `mute` | Writes a mute for the clicker. Scope picks the snapshot field (`gym` → `gym_id`, `pokemon` → `pokemon_id`, `area` → first matched area, `everything` → user-wide). `params.duration_min` (default 60). |
| `unsubscribe` | v1 only accepts `scope = "tracking"`; full rule-UID unsubscribe is a follow-up. |
| `redeliver` | DM the alert to the clicker. v1 sends a stub confirmation; full re-render is a follow-up. |
| `render` | Render a different template id (`params.template_id`) as the click response. Useful for "show me the PVP detail" without an inline body. |

### `show_if` — Conditional Visibility

`show_if` is a Handlebars expression evaluated against the resolved view at render time. The button is attached only when the expression is truthy. Use it for buttons that only make sense on certain alerts:

```json
{ "id": "pvp", "label": "Show PVP details", "show_if": "{{pvpAvailable}}", "response_template_inline": "..." }
```

### `visible_to` — Access Control

| Value | Meaning |
|---|---|
| `anyone` (default) | Anyone with channel-view permission can click. |
| `registered` | The clicker must have a Poracle account. Good for buttons that reveal precise coords or address. |
| `admin` | The clicker must be in `[discord] admins`. On DMs the button is hidden entirely for non-admins; on channels every viewer sees the button but non-admin clicks are rejected. |

### Click-time Failure Messages

These messages surface as ephemeral replies in Discord — operators should know what each means:

| Message | Cause |
|---|---|
| `This alert has expired.` | No snapshot found for the message ID. Either `[snapshots] enabled = false`, the snapshot's TTL passed, or the safety sweep already cleaned it. |
| `This button is no longer available.` | The DTS entry the button came from was removed or renamed since the alert was sent. |
| `This button doesn't apply here.` | The `applies_to` set doesn't include the destination type. Usually shouldn't happen — the renderer filters at attachment time. |
| `This button isn't for you.` | `visible_to` check failed (e.g. a non-admin clicked an `admin`-only button on a channel alert). |
| `Slow down — try that again in a moment.` | Click cooldown — same user / same button / within debounce window. |

### Worked Example: Monster Card with Four Buttons

A working end-to-end example covering inline response templates, action buttons, and gated visibility lives at [`examples/dts/buttons/monster-with-pvp.toml`](https://github.com/jfberry/PoracleNG/blob/main/examples/dts/buttons/monster-with-pvp.toml) in the PoracleNG repo. It demonstrates:

- **"Show PVP details"** — inline response template, gated with `show_if: "{{pvpAvailable}}"`.
- **"Show map links"** — `visible_to: "registered"` so precise location stays one click away for registered users only.
- **"Copy coordinates"** — plain-text `response_text` with raw lat/lon for paste.
- **"Mute this species"** — `action: "mute"`, scope `pokemon`, default DM-only `applies_to`.

### Editor Support

`GET /api/dts/actions` returns each action's `name`, accepted `scopes`, whether a scope is required, and the `params` keys the handler accepts. The PoracleWeb editor uses this to drive its button-authoring UI. See [`API.md`](https://github.com/jfberry/PoracleNG/blob/main/API.md) in the PoracleNG repo for the full action registry endpoint.


## Tips

- Test your changes with `!poracle-test` (see [Testing Templates](dts.md#testing-templates)).
- Use `{{round iv}}` rather than `{{iv}}`, which has two decimal places.
- Use `{{#if field}}` around optional fields.
- `formNormalised` is already empty for Normal forms, so you don't need `{{#isnt formName 'Normal'}}`.
- Check helper names against the [Helper Reference](#helper-reference). A misspelt helper prints nothing.
- Discord limits embed titles to 256 characters, descriptions to 4096 and footers to 2048. Longer text is cut off.
- Use `{{> partialName}}` and `@include` to share snippets between templates.
