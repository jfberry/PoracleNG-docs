# DTS editor API

These endpoints let an editor — such as the [Poracle Config UI](../configuration/config-ui.md) — read, preview, save and test [DTS message templates](../configuration/dts.md) without shell access. All need `X-Poracle-Secret`. The `/api/dts/*` endpoints only exist when the DTS renderer started successfully.

## Overview

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/dts/templates` | Template entries with full content |
| `POST` | `/api/dts/templates` | Save one or more entries |
| `DELETE` | `/api/dts/templates` | Delete one entry |
| `PUT` | `/api/dts/templates/file` | Overwrite the Handlebars file behind a `templateFile` entry |
| `GET` / `POST` | `/api/dts/reload` | Reload templates and partials from disk |
| `GET` | `/api/config/templates` | Template metadata only (names per type/platform/language) |
| `GET` | `/api/dts/fields` | DTS type names that have field metadata |
| `GET` | `/api/dts/fields/{type}` | Fields, block scopes and snippets for one type |
| `GET` | `/api/dts/partials` | Handlebars partials |
| `GET` | `/api/dts/emoji` | Emoji lookup map |
| `GET` | `/api/dts/custommaps` | Custom maps for `{{map}}` (5.3+) |
| `GET` | `/api/dts/testdata` | Sample webhook scenarios |
| `POST` | `/api/dts/enrich` | Run a webhook through enrichment and return the template variables |
| `POST` | `/api/dts/render` | Render a stored template with given variables |
| `POST` | `/api/dts/sendtest` | Render a template and deliver it to a user/channel |
| `GET` | `/api/dts/actions` | Available alert-button actions |

## Reading templates

### `GET /api/dts/templates`

Filter with any of `type`, `platform`, `language`, `id`.

```sh
curl -H "X-Poracle-Secret: your-secret" \
  "http://localhost:3030/api/dts/templates?type=monster&platform=discord"
```

```json
{
  "status": "ok",
  "templates": [
    {"id": "1", "type": "monster", "platform": "discord", "language": "", "default": true, "readonly": true,
     "template": {"embed": {"title": "{{round iv}}% {{fullName}}"}}},
    {"id": "1", "type": "fort-update", "platform": "discord", "language": "", "default": true,
     "templateFile": "dts/fort_update.txt", "templateFileContent": "{{#eq fortType 'pokestop'}}…{{/eq}}"}
  ]
}
```

- `readonly: true` marks entries from the bundled `fallbacks/`. Once you have any editable entry for a type/platform, the fallback entries for that combination are hidden.
- Entries that point at an external Handlebars file have `templateFile` plus the file's text in `templateFileContent`; edit those with `PUT /api/dts/templates/file`.

### `GET /api/config/templates`

Lighter metadata for pickers (e.g. PoracleWeb's template dropdown): available template ids by type, platform and language, without the template bodies. `?includeDescriptions=true` adds names and descriptions.

## Saving templates

### `POST /api/dts/templates`

Body is an array of entries. `type` and `platform` are required (platform may be empty only for platform-independent types such as `help`).

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  http://localhost:3030/api/dts/templates \
  -d '[{"id": "1", "type": "monster", "platform": "discord", "language": "en",
        "template": {"embed": {"title": "{{round iv}}% {{fullName}}"}}}]'
```

```json
{"status": "ok", "saved": 1}
```

How saves land on disk:

- Each entry is written to its own file `config/dts/{type}-{id}[-{platform}][-{language}].json`, and removed from wherever it lived before (e.g. `config/dts.json`), leaving other entries in that file alone.
- If the entry currently comes from a **TOML** file in `config/dts/`, it's written back to that TOML file instead, preserving the format.
- Saving over a read-only fallback creates an override in `config/dts/`; the fallback itself isn't modified.

Changes are live immediately — no reload needed.

### `DELETE /api/dts/templates?type=&platform=&id=&language=`

Removes the entry from memory and its source file. `type`, `platform` and `id` are required (`400` otherwise). `404` if no such entry; `403` for read-only fallbacks.

### `PUT /api/dts/templates/file?type=&platform=&id=&language=`

Replaces the Handlebars text behind a `templateFile` entry. The file path is looked up from the entry — clients can't supply a path.

```sh
curl -X PUT -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  "http://localhost:3030/api/dts/templates/file?type=fort-update&platform=discord&id=1" \
  -d '{"content": "{{#eq fortType '\''pokestop'\''}}…{{/eq}}"}'
```

```json
{"status": "ok", "templateFile": "dts/fort_update.txt", "backup": "backups/dts/fort_update.txt.bak.2026-10-17_101500"}
```

The old file is backed up to `config/backups/` first. `404` unknown entry, `400` if the entry uses an inline template, `403` for read-only entries.

### `GET` or `POST /api/dts/reload`

Re-reads `config/dts.json`, `config/dts/`, partials and the fallbacks. Use it after editing template files by hand. Returns `{"status":"ok"}`.

## Editor helpers

### `GET /api/dts/fields/{type}`

Everything an editor needs for autocomplete on one DTS type (`monster`, `monsterNoIv`, `raid`, `egg`, `quest`, `invasion`, `lure`, `nest`, `gym`, `fort-update`, `maxbattle`, `weatherchange`, `greeting`, …; list them with `GET /api/dts/fields`).

```json
{
  "status": "ok",
  "type": "monster",
  "fields": [
    {"name": "fullName", "type": "string", "description": "Name + form combined", "category": "identity", "preferred": true},
    {"name": "pokemon_id", "type": "int", "description": "Pokemon ID (webhook)", "category": "identity", "rawWebhook": true, "preferredAlternative": "pokemonId"}
  ],
  "blockScopes": [{"helper": "each", "args": ["pvpGreat"], "iterableFields": ["pvpGreat", "pvpUltra", "pvpLittle"], "fields": ["…"]}],
  "snippets": [{"label": "Countdown", "insert": "<t:{{despawnTimestamp}}:R>", "category": "pokemon", "platform": "discord"}]
}
```

`deprecated` fields should be hidden or flagged; `rawWebhook` fields have a nicer enriched equivalent.

### `GET /api/dts/partials`, `/api/dts/emoji`, `/api/dts/custommaps`

For reproducing server rendering in the browser:

- **partials** → `{"status": "ok", "partials": {"name": "handlebars source", …}}`
- **emoji** → with `?platform=discord`: `{"status", "platform", "emoji": {key: value}}`, the merged map the renderer uses. Without `platform`: `{"status", "defaults", "platforms": {"discord": {…}, "telegram": {…}}}` so a UI can show which keys are customised.
- **custommaps** (5.3+) → `{"status": "ok", "maps": {"name": {…}, "name.de": {…}}}` from `config/customMaps/`. `{{map}}`/`{{map2}}` try `name.<user language>` then `name`. Maps load at startup.

### `GET /api/dts/testdata`

The named sample webhooks from `fallbacks/testdata.json` merged with your `config/testdata.json` — the same scenarios `!poracle-test` uses.

```json
{
  "status": "ok",
  "testdata": [{"type": "pokemon", "test": "hundo", "location": "current", "webhook": {"pokemon_id": 149, "…": "…"}}],
  "types": {"monster": {"…": "…"}, "monsterChanged": {"…": "…"}}
}
```

Filter with `?dtsType=` (a DTS type name such as `monster`, `invasion`, `incident`; matching entries are tagged with `dtsType`) or the older `?type=` (a raw webhook type such as `pokemon`, `pokestop`). `types` maps every DTS type to its source webhook type.

### `GET /api/dts/actions`

Alert-button actions an editor can offer (mute, unsubscribe, redeliver, render, …), with the scopes each accepts, whether a scope is required, and the params it reads:

```json
{"actions": [{"name": "mute", "scopes": ["…"], "required_scope": true, "params": ["duration_min"]}]}
```

See the buttons section of the DTS docs for how buttons are configured.

## Previewing and testing

### `POST /api/dts/enrich`

Runs a raw webhook through the full enrichment pipeline and returns the variable map templates see.

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  http://localhost:3030/api/dts/enrich \
  -d '{"type": "pokemon", "language": "en", "platform": "discord",
       "webhook": {"pokemon_id": 129, "latitude": 51.28, "longitude": 1.08, "disappear_time": 9999999999,
                   "individual_attack": 15, "individual_defense": 15, "individual_stamina": 15}}'
```

```json
{"status": "ok", "variables": {"name": "Magikarp", "fullName": "Magikarp", "iv": 100, "cp": 212, "pvpGreat": ["…"], "…": "…"}}
```

`type` is a webhook type (`pokemon`, `raid`, `quest`, `pokestop`, `gym`, `nest`, `fort_update`, `max_battle`, …) or, where `derivedDtsTypes` is advertised in `/health`, a DTS type name (`monsterChanged`, `incident`, `questSummary`, …). `language` defaults to `en`, `platform` to `discord`.

### `POST /api/dts/render`

Renders a **stored** template with variables you supply:

```json
{"type": "monster", "id": "1", "platform": "discord", "language": "en", "view": {"fullName": "Magikarp", "iv": 100}}
```

Returns `{"status": "ok", "message": <rendered JSON>}`. `404` if no template matches.

### `POST /api/dts/sendtest`

Renders a template you supply (string or object, e.g. unsaved editor content) with the given variables and **delivers** it, so you can see exactly what Discord or Telegram shows.

```json
{
  "template": {"embed": {"title": "{{round iv}}% {{fullName}}"}},
  "variables": {"fullName": "Magikarp", "iv": 100},
  "target": {"id": "123456789012345678", "type": "discord:user"},
  "platform": "discord",
  "language": "en"
}
```

`target.type` defaults to `discord:user`. Returns `{"status": "ok", "message": "sent"}`; `400` for a missing template/target or a template that won't compile.

To push a whole webhook through enrichment and rendering to a user, use [`POST /api/test`](messaging.md#test-alerts) instead.
