# Alert Templates (DTS)

DTS (Dynamic Template System) controls how alert messages look on Discord and Telegram. Templates are written in [Handlebars](https://handlebarsjs.com/), and each one renders to a JSON message (a Discord message or a Telegram message definition).

- This page covers template files, how a template is chosen, message formats and testing.
- [DTS Field Reference](dtsfields.md) lists every field available for each alert type.
- [Advanced DTS](dtsadvanced.md) covers helpers, custom maps, emoji, multiple languages and buttons.

## Template Files

Templates are loaded in this order:

1. `fallbacks/dts.json` and `fallbacks/dts/**/*.json`: the bundled defaults (read-only, shipped with the code)
2. `config/dts.json`: your main template file (optional)
3. `config/dts/*.json` and `config/dts/*.toml`: additional files, in directory order

Your entries (steps 2 and 3) **always** beat the bundled ones. Among your own entries, the one loaded last wins, so an entry in `config/dts/` overrides the same entry in `config/dts.json`. To customise a bundled template, copy it into `config/dts/` and edit the copy.

The `examples/dts/` directory has community templates you can use as a starting point.

After editing, reload templates without restarting by running `!poracle-admin reload dts`, or calling `GET`/`POST /api/dts/reload`.

## Entry Structure

A JSON DTS file is an array of entries:

```json
[
  {
    "id": "1",
    "type": "monster",
    "platform": "discord",
    "language": "en",
    "default": true,
    "template": {
      "embed": {
        "title": "{{round iv}}% {{fullName}}",
        "description": "Ends {{time}}"
      }
    }
  }
]
```

| Field | Description |
|---|---|
| `type` | Alert type (see [Alert Types](#alert-types)) |
| `id` | Template id. Users choose it with `template:<id>`. Matching ignores case. |
| `platform` | `discord` or `telegram`. Leave it empty (`""`) to serve both platforms; that only applies to `help` entries. |
| `language` | Language code, e.g. `en` or `de`. Leave it empty to match any language. |
| `default` | Use this entry when the requested id doesn't exist (see [Template Selection](#template-selection)) |
| `template` | The message, written as a JSON object or as a string. In an object, any **array of strings** is joined together (with no separator) before rendering, so you can split long text over several lines. |
| `templateFile` | Instead of `template`, a file path relative to `config/`. The file's contents are the raw Handlebars template. |
| `name`, `description` | Labels shown to users when they list templates |
| `hidden` | Leave this template out of the user-facing list |
| `buttons` | Discord buttons (see [Interactive Buttons](dtsadvanced.md#interactive-buttons)) |

### Template IDs

Each alert type can have several templates with different ids. Users pick one when tracking:

```
!track pikachu template:2
```

If a rule has no template, `[general] default_template_name` is used (default `"1"`).

### Template Selection

The template for each recipient is chosen from their alert type, platform, template id and language. PoracleNG checks your entries first, and only looks at the bundled entries if none of yours match. Within each set, it tries these steps in order:

1. Same type + id + platform + the user's language
2. Same type + id + platform, with an entry that has no language
3. A `default` entry for the type + platform in the user's language
4. A `default` entry for the type + platform with no language
5. A `default` entry for the type + platform in `[general] locale`
6. Any `default` entry for the type + platform

If several entries match at the same step, the last one loaded wins. If nothing matches the user's platform, the steps are repeated for entries with `platform: ""`.

Template **ids don't carry over between languages**. If a German user asks for `template:2` and you only have an English `2`, they get the German **default** template, not your English `2`. Give every language each id you use, or give the template no language. A missing `monsterNoIv` template falls back to `monster`.

!!! warning
    On the current code, a default entry with **no** language beats a default entry in the user's language (step 4 overrides step 3). If you have both, remove `"default": true` from the one without a language.

If nothing matches at all, the message reads `Template not found: type/platform/id/language`. The same message is sent if the template fails to render or produces invalid JSON, and the admin channel is notified.

### The `default: true` Gotcha for Help Entries

`default: true` means "use this entry for any id of this type, platform and language that has no exact match". For most types that's harmless. For `type: "help"` it has an unexpected effect:

- `!help` asks for id `"index"`.
- `!help track` asks for id `"track"`.
- `!help raid` asks for id `"raid"`, and so on.

If your custom help entry has `"default": true` with any id (e.g. `"1"`), it matches **every one of those requests** and hides the bundled per-topic help entries.

| Intent | Config |
|---|---|
| My entry is the complete help (it replaces every topic too) | `{"id": "<anything>", "default": true}` |
| My entry is the landing page, and the bundled `!help track` / `!help raid` / … still work | `{"id": "index", "default": false}` |

PoracleNG logs a warning at startup when it finds a `type: "help"` entry of yours with `default: true`.

## TOML Format

`config/dts/*.toml` files are loaded alongside JSON. Each `[[entry]]` is one entry with the same fields. TOML's multi-line strings make long templates much easier to write than escaped JSON:

```toml
[[entry]]
id          = "1"
type        = "monster"
platform    = "discord"
language    = "en"
description = "Compact monster card"

template = """
{
  "embed": {
    "title": "{{fullName}} {{round iv}}%",
    "color": "{{ivColor}}"
  }
}
"""
```

A **string** template (as in this example) is used as raw Handlebars, exactly like `templateFile`. Buttons use `[[entry.buttons]]` (see [Interactive Buttons](dtsadvanced.md#interactive-buttons)).

### Config Editor Round-trip

The config editor works in JSON internally and saves each file back in its original format. **Comments and your key ordering are not kept** when the editor saves, because the whole file is rewritten. Multi-line template strings (`"""..."""`) **are** kept.

Each save first writes a backup to `config/backups/`, so you can recover from a bad save. If you want to keep comments in a hand-written TOML file, edit it directly and reload instead of using the editor.

### Per-template Save Behaviour

`POST /api/dts/templates` writes each saved template to **its own file** in `config/dts/` (e.g. `monster-1-discord-en.toml`). If the template was previously in `config/dts.json` or another file, it's removed from that file, and the file's other entries are left alone. A file that ends up empty is deleted, except `config/dts.json`. For the full editor API, see [`API.md`](https://github.com/jfberry/PoracleNG/blob/main/API.md) in the PoracleNG repo.

## Alert Types

| Type | Sent for | Notes |
|------|---------|-------|
| `monster` | Pokemon with IVs | |
| `monsterNoIv` | Pokemon without IVs | Falls back to `monster` |
| `monsterChanged` | Reply when a tracked encounter changes species, form or stats | Uses the same template id as the original alert |
| `raid` | Raid boss | |
| `egg` | Raid egg | `{{time}}` is the hatch time |
| `rsvpChanges` | Short follow-up when raid RSVP counts change | Optional. Only used if an entry exists with the user's exact template id. |
| `quest` | Field research | |
| `questSummary` | Grouped quest digest | For quest rules tracked with `summary` |
| `invasion` | Team Rocket invasion | |
| `incident` | Gold-Stop, Kecleon | |
| `showcase` | Pokémon Showcase | Tracked as an incident |
| `lure` | Lure module | |
| `nest` | Nest change | |
| `gym` | Gym team, slot or battle change | |
| `maxbattle` | Max Battle at a Power Spot | |
| `weatherchange` | Weather change affecting a user's tracked pokemon | |
| `fort-update` | Pokestop or gym added, edited or removed | |
| `greeting` | Welcome message when a user registers | Fields: `{{prefix}}` only |
| `help` | `!help` and `!help <topic>` | Fields: `{{prefix}}`, `{{userIsAdmin}}` |
| `buttonResponse` | Button click reply, chosen by `response_template_id` | Same fields as the alert snapshot |

## Writing Templates

### Escaping

Handlebars HTML escaping is **turned off**: `{{x}}` and `{{{x}}}` print exactly the same thing. Triple braces still work and are harmless. If you need HTML escaping (for example inside a URL you build), use `{{escape x}}`.

Free text that comes from the scanner or a geocoder is cleaned before it reaches your template, so it can't break the JSON: `"` becomes `''`, `\` becomes `?`, and newlines become spaces. This applies to:

- pokestop, gym, nest and station names (`pokestop_name`, `pokestopName`, `gym_name`, `nest_name`, `name`, and `station_name` on **5.3+**)
- fort descriptions and the old/new values in `fort-update`
- address fields (`addr`, `formattedAddress`, `streetName`, `city` and so on)
- on **5.3+**, each pokestop name in a `questSummary`

Other raw webhook fields are **not** cleaned.

### Missing fields and helpers

- A field that doesn't exist prints nothing.
- An **unknown helper** (e.g. `{{sum a b}}`) also prints nothing. No error is shown, so check spelling against the [helper reference](dtsadvanced.md#helper-reference).
- A known helper called with **too few arguments** (e.g. `{{add 1}}`, or `{{#pokemon id}}` without a form) makes the whole render fail. The user gets the fallback `Template not found` message and the admin channel is notified.
- On **5.3+**, passing a missing field to a helper (e.g. `{{concat a missing}}`) treats it as empty. Older versions printed `[]`.

### Partials

Partials are reusable snippets defined in `config/partials.json` (or `fallbacks/partials.json` if you don't have one). They're available in every template:

```json
{
  "remainingTime": "{{#if tthh}}{{tthh}}h {{/if}}{{tthm}}m {{tths}}s"
}
```

```
Time left: {{> remainingTime}}
```

### `@include` and `templateFile`

You can keep template text in separate files:

- `"templateFile": "dts/monster-card.hbs"` loads the whole template from a file (path relative to `config/`). The file is raw Handlebars, so it may contain things that aren't valid JSON, such as `"color": {{#eq teamId 1}}255{{else}}0{{/eq}}`.
- `@include filename` inside any template string is replaced with the contents of `config/dts/filename`. Everything after `@include ` up to the end of the line is the file name.

### Short Links

Wrap a URL in `<S<` … `>S>` to shorten it with [Shlink](https://shlink.io/):

```
[Map](<S<{{{googleMapUrl}}}>S>)
```

Shortening only happens when `[general] shortlink_provider = "shlink"` and `shortlink_provider_url` are set. `shortlink_provider_key` and (optionally) `shortlink_provider_domain` are also used. With no provider (or any other provider, such as `hideuri` or `yourls`), the markers are removed and the full URL is left in place.

## Discord Message Format

A Discord template renders to a Discord message object:

```json
{
  "content": "Optional text above the embed",
  "embed": {
    "color": "{{ivColor}}",
    "title": "{{round iv}}% {{fullName}} cp:{{cp}} L:{{level}} {{atk}}/{{def}}/{{sta}} {{boostWeatherEmoji}}",
    "url": "{{googleMapUrl}}",
    "description": "End: {{time}}, Time left: {{tthm}}m {{tths}}s\n{{addr}}\nquick: {{quickMoveName}}, charge: {{chargeMoveName}}",
    "thumbnail": { "url": "{{imgUrl}}" },
    "image": { "url": "{{staticMap}}" },
    "footer": { "text": "{{areas}}" }
  }
}
```

- Use `embed` (one embed) or `embeds` (an array of up to 10). A single `embed` is turned into `embeds` before sending.
- `color` can be a number, a hex string (`"FF0000"` or `"#FF0000"`), or a decimal string. Any other value is dropped.
- Text is trimmed to fit Discord's limits: content 2000, title 256, description 4096, 25 fields, field value 1024, footer 2048, and 6000 characters for the whole embed. Empty embeds and empty content are removed.
- If an embed `image` is your static map and `[discord] upload_embed_images` is on, the image is uploaded to Discord as a file instead of linked.

## Telegram Message Format

```json
{
  "content": "*{{round iv}}% {{fullName}}* cp:{{cp}}\n{{addr}}\n[Google]({{googleMapUrl}})",
  "sticker": "{{stickerUrl}}",
  "photo": "{{staticMap}}",
  "parse_mode": "Markdown",
  "webpage_preview": false,
  "location": true,
  "send_order": ["sticker", "photo", "text", "location", "venue"]
}
```

| Field | Description |
|---|---|
| `content` | Message text. Empty markdown links and `null`/`undefined` words are removed before sending. |
| `sticker` | Sticker image URL (use `stickerUrl`, which is webp) |
| `photo` | Photo URL, e.g. the static map |
| `parse_mode` | `Markdown` (default), `MarkdownV2` or `HTML`. Case doesn't matter. |
| `webpage_preview` | `true` to show link previews (default off) |
| `location` | `true` to also send a location pin for the alert |
| `venue` | `{"title": "...", "address": "..."}`: sends a venue (pin with a title) at the alert location |
| `send_order` | Order of the parts. Default `sticker, photo, text, location, venue`. Can be an array or a string separated by `,`, `|` or `;`. Parts that are empty are skipped. |

Each part is a separate Telegram message. When an alert is cleaned up, all of its parts are deleted, and an edit updates the text part.

## Discord Countdown Timers

Discord can show live countdowns with the `<t:UNIX:R>` format:

| Alert Type | Field | Snippet |
|---|---|---|
| Pokemon | `{{despawnTimestamp}}` | `<t:{{despawnTimestamp}}:R>` |
| Raid | `{{endTimestamp}}` | `<t:{{endTimestamp}}:R>` |
| Egg | `{{hatchTimestamp}}` | `<t:{{hatchTimestamp}}:R>` |
| Invasion / Incident / Showcase | `{{expirationTimestamp}}` | `<t:{{expirationTimestamp}}:R>` |
| Lure | `{{expirationTimestamp}}` | `<t:{{expirationTimestamp}}:R>` |
| MAX Battle | `{{endTimestamp}}` | `<t:{{endTimestamp}}:R>` |

## Examples

### Monster Alert with PVP Rankings

```json
{
  "embed": {
    "color": "{{ivColor}}",
    "title": "{{round iv}}% {{fullName}} cp:{{cp}} L:{{level}} {{atk}}/{{def}}/{{sta}} {{boostWeatherEmoji}}",
    "description": "End: {{time}}, Time left: {{tthm}}m {{tths}}s \n {{#if weatherChange}}{{weatherChange}}\n{{/if}}{{#if futureEvent}}There is an event ({{futureEventName}}) {{#eq futureEventTrigger 'start'}}starting{{else}}ending{{/eq}} at {{futureEventTime}}.\n{{/if}}{{addr}} \n quick: {{quickMoveName}}, charge: {{chargeMoveName}} \n{{#if pvpGreat}}**Great league:**\n{{#each pvpGreat}} - {{fullName}} #{{rank}} @{{cp}}CP (Lvl. {{levelWithCap}})\n{{/each}}{{/if}}{{#if pvpUltra}}**Ultra league:**\n{{#each pvpUltra}} - {{fullName}} #{{rank}} @{{cp}}CP (Lvl. {{levelWithCap}})\n{{/each}}{{/if}} Maps: [Google]({{googleMapUrl}}) | [Apple]({{appleMapUrl}})",
    "thumbnail": { "url": "{{imgUrl}}" },
    "image": { "url": "{{staticMap}}" }
  }
}
```

### Invasion with Conditional Rewards

```json
{
  "embed": {
    "title": "Team Rocket at {{pokestopName}}",
    "color": "{{gruntTypeColor}}",
    "description": "Type: {{gruntTypeName}} {{gruntTypeEmoji}}\nGender: {{genderData.name}}{{genderData.emoji}}\nPossible rewards: {{#compare gruntRewardsList.first.chance '==' 100}}{{#forEach gruntRewardsList.first.monsters}}{{this.name}}{{#unless isLast}}, {{/unless}}{{/forEach}}{{/compare}}{{#compare gruntRewardsList.first.chance '<' 100}}\n ‣ {{gruntRewardsList.first.chance}}% : {{#forEach gruntRewardsList.first.monsters}}{{this.name}}{{#unless isLast}}, {{/unless}}{{/forEach}}\n ‣ {{gruntRewardsList.second.chance}}% : {{#forEach gruntRewardsList.second.monsters}}{{this.name}}{{#unless isLast}}, {{/unless}}{{/forEach}}{{/compare}}\n Ends: {{time}}, in ({{#if tthh}}{{tthh}}h {{/if}}{{tthm}}m {{tths}}s)"
  }
}
```

### Weather Change with Affected Pokemon

```json
{
  "embed": {
    "title": "⚠️ Weather change ⚠️",
    "description": "{{#if oldWeatherName}}It went from {{oldWeatherName}} {{oldWeatherEmoji}} to {{else}}It is now {{/if}}{{weatherName}} {{weatherEmoji}}\n{{#if enrichedActivePokemons}}The following Pokémon have changed:\n{{#each enrichedActivePokemons}}**{{fullName}}** - {{round iv}}% - {{cp}}CP\n{{/each}}{{else}}This could have altered reported stats and IV...{{/if}}"
  }
}
```

`activePokemons` is only filled when `[weather] show_altered_pokemon_static_map = true`. `enrichedActivePokemons` is always filled.

## Testing Templates

Admins can send themselves a test alert with `!poracle-test`:

```
!poracle-test <type> [test] [template:<id>] [language:<code>]
```

- `<type>` is one of `pokemon`, `raid`, `pokestop`, `incident`, `gym`, `nest`, `quest`, `quest-summary`, `monster-changed`, `rsvp-changes`, `fort-update`, `max-battle`, `showcase` or `weatherchange`. DTS type names such as `monster`, `egg`, `lure` or `invasion` also work.
- Leave out `[test]` to list the sample events available for that type, e.g. `!poracle-test raid` lists `egg1`, `egg5`, `level1`, `level5`, …
- `!poracle-test pokemon hundo template:2` renders sample `hundo` with your template `2`.

The samples come from `fallbacks/testdata.json`. Entries in `config/testdata.json` with the same `type` + `test` replace the bundled ones, and new entries are added to the list. Samples are moved to your registered location unless the entry has `"location": "keep"`, and their timestamps are refreshed so the alert isn't already expired. A `raid` sample without `pokemon_id` renders as an `egg`, and a `pokestop` sample with `lure_expiration` renders as a `lure` (otherwise an `invasion`).

For helpers, custom maps, emoji and multi-language setups, see [Advanced DTS](dtsadvanced.md).
