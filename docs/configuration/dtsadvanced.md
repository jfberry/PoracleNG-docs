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
