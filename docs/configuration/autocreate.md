# Channel Auto-create

Auto-create lets PoracleNG build Discord channels — and optionally categories, private threads, and a "click to join" picker post — from a reusable JSON template. There are two ways to invoke it:

- **Interactive, one area at a time.** Run `!autocreate <templateName> <args...>` in a Discord channel where you have admin permission. One call → one set of channels, threads, and picker. Good for occasional, ad-hoc setup.
- **Bulk sync, many areas at once.** Configure rules in `config.toml` that map a template across every geofence in your area pack, then run `!autocreate sync` (or `POST /api/autocreate/run`) to create / reuse / remove channels in one pass. Good for large servers that want a consistent layout across hundreds of fences.

Both modes share the same `config/channelTemplate.json` file. Whatever you can build interactively, you can build in bulk.

!!! tip "Visual editor available"
    The [Poracle Config UI](config-ui.md#the-auto-create-editor) has a visual editor for `channelTemplate.json` — typed form fields, validation, and a tree view of categories / channels / threads / pickers. Recommended over hand-editing the JSON for any non-trivial template.

## The Template File

Templates live in `config/channelTemplate.json` — a JSON array, each entry a named template:

```json
[
  {
    "name": "area",
    "definition": {
      "category": { },
      "channels": [ ]
    }
  }
]
```

| Field | Required | Description |
|---|---|---|
| `name` | yes | Unique identifier referenced by `!autocreate <name>` and `[[autocreate.rules]] template = "<name>"`. No spaces. |
| `definition.category` | no | If present, all channels in this template land under one Discord category. If omitted, channels are created at the guild's top level. |
| `definition.channels` | yes | At least one channel entry. |

### Category

```json
"category": {
  "categoryName": "{{group}}",
  "roles": [
    { "name": "@everyone", "view": false },
    { "name": "subscribers", "view": true }
  ]
}
```

| Field | Description |
|---|---|
| `categoryName` | Display name of the Discord category. Supports `{0}`, `{1}` placeholders. |
| `roles` | Permission overwrites for the category itself. See [Role permission flags](#role-permission-flags). |

If a category with the same name already exists in the guild, auto-create reuses it instead of creating a duplicate. Channels created by this template join that existing category.

### Channels

```json
"channels": [
  {
    "channelName": "{{name}}",
    "channelType": "text",
    "topic": "Pokémon notifications for {0}",
    "controlType": "bot",
    "webhookName": "",
    "roles": [ ],
    "commands": [ "area add {0}", "track 25 great5" ],
    "threads": [ ],
    "threadPicker": { }
  }
]
```

| Field | Required | Default | Description |
|---|---|---|---|
| `channelName` | yes | — | Discord channel name. Discord forces lowercase. Supports placeholders. |
| `channelType` | no | `"text"` | `"text"` or `"voice"`. Voice channels can't have topics, commands, threads, or pickers — those fields are ignored if you set them. |
| `topic` | no | `""` | Channel topic. Supports placeholders. |
| `controlType` | no | `""` | What kind of Poracle control this channel gets. See below. |
| `webhookName` | no | `"Poracle"` | Webhook name when `controlType` is `"webhook"`. Currently fixed; leave empty. |
| `roles` | no | — | Permission overwrites on the channel itself. |
| `commands` | no | — | Poracle `!` commands to run as this channel at creation. |
| `threads` | no | — | Private threads to create under this channel. |
| `threadPicker` | no | — | A click-to-join embed posted in this channel for the threads. |

### controlType

| Value | Behaviour |
|---|---|
| `""` (empty) | No Poracle tracking on the channel. The channel exists in Discord but Poracle doesn't send anything to it. Use for separator / info channels. |
| `"bot"` | Poracle treats the channel itself as a target. A `humans` row of type `discord:channel` is registered, keyed by the channel ID. Tracking commands in `commands[]` apply to it. |
| `"webhook"` | Poracle creates a webhook on the channel named `Poracle` and registers a `humans` row keyed by the webhook URL. Webhook delivery is faster (no per-channel rate limits) and avoids the "bot must be present in the channel" requirement; useful for high-volume alert channels. |

### commands

Each entry runs through Poracle's command parser as if the channel/webhook target had typed it:

```json
"commands": [
  "area add downtown",
  "track 25 great5 ultra15",
  "raid level5"
]
```

The configured `[discord]` prefix (typically `!`) is auto-prepended.

When commands run:

- **On fresh channels (newly created):** always.
- **On reused channels:** only when the caller asked for reset semantics.
    - Interactive `!autocreate area Foo` re-runs commands on a reused channel — wipes existing tracking first, then re-applies the template.
    - Bulk sync **leaves reused-channel tracking alone by default**. This is deliberate: an admin who tweaked tracking on one channel doesn't want the next scheduled sync to undo their work. Add the `reset` keyword to the trigger if you do want to re-apply.

### Threads

Optional. Each entry is one private thread under the parent channel.

```json
"threads": [
  {
    "name": "iv100-{{name}}",
    "buttonLabel": "100% IVs",
    "buttonStyle": "success",
    "commands": [ "area add {0}", "track everything iv100" ]
  }
]
```

| Field | Required | Description |
|---|---|---|
| `name` | yes | Thread name. Also the default button label. Supports placeholders. |
| `buttonLabel` | no | Override for the picker button label. Supports placeholders. |
| `buttonStyle` | no | `"primary"` (blue), `"secondary"` (grey, default), `"success"` (green), or `"danger"` (red). |
| `commands` | no | Commands run as the thread (a `humans` row of type `discord:thread`). |

Threads require `MANAGE_THREADS` on the parent channel for the bot. Each thread becomes a `humans` row of type `discord:thread`. A background sweeper unarchives threads on a schedule (controlled by `[discord] thread_keep_alive_interval_hours`) so they stay accessible without anyone posting in them.

### Thread Picker

Optional. When set, an embed plus a row of buttons is posted in the parent channel. Clicking a button adds the user to the corresponding private thread.

```json
"threadPicker": {
  "embedTitle": "Choose your alert level for {0}",
  "embedDescription": "Click a button below to join that thread.",
  "pinned": true
}
```

| Field | Description |
|---|---|
| `embedTitle` | Embed title. Supports placeholders. |
| `embedDescription` | Embed description. Supports placeholders. |
| `pinned` | Pin the picker message in the channel. Default `false`. |

Notes:

- 5 buttons per row, 5 rows per message — for >25 threads, multiple picker messages are emitted in sequence.
- The picker is idempotent: re-running auto-create edits the existing picker if it can find it (cached), posts fresh if it can't.
- Buttons survive bot restarts — their `custom_id` is stateless.

## Role Permission Flags

Every `roles[]` entry on a category, channel, or webhook block sets one role's permission overwrites:

```json
{
  "name": "subscribers",
  "view": true,
  "send": false,
  "react": null
}
```

- `name` — the Discord role name. Case-insensitive match. Special: `@everyone` resolves to the guild's `@everyone` role.
- Each flag is **tri-state**:
    - `true` — allow
    - `false` — deny
    - `null` or absent — inherit (don't set this overwrite at all)

If the named role doesn't exist in the guild, auto-create creates it (skipped on dry-run).

### Available Flags

**General:** `view` `viewHistory` `send` `react` `pingEveryone` `embedLinks` `attachFiles` `sendTTS` `externalEmoji` `externalStickers` `createPublicThreads` `createPrivateThreads` `sendThreads` `slashCommands` `createInvite`

**Voice:** `connect` `speak` `autoMic` `stream` `vcActivities` `prioritySpeaker` `mute` `deafen` `move`

**Admin (use sparingly):** `channels` (Manage Channels) · `messages` (Manage Messages) · `roles` (Manage Roles) · `webhooks` (Manage Webhooks) · `threads` (Manage Threads) · `events` (Manage Events)

### Common Pattern: Hide From Everyone Except One Role

```json
"roles": [
  { "name": "@everyone", "view": false },
  { "name": "premium", "view": true, "send": true, "react": true }
]
```

`@everyone` is denied view; `premium` is allowed. Result: only premium members see the channel.

## Placeholders

`{0}`, `{1}`, ... substitute positional arguments into any string that supports them: `categoryName`, `channelName`, `topic`, every `commands[]` entry, every `threads[].name` and `threads[].buttonLabel`, `threadPicker.embedTitle`, and `threadPicker.embedDescription`.

### How Placeholders Are Filled

**Interactive (`!autocreate <templateName> <args...>`):**

`{0}` corresponds to the **first arg after the template name**.

```
!autocreate area downtown brussels
```

→ `{0}` is `downtown`, `{1}` is `brussels`. (The template name itself doesn't count. This matches the original PoracleJS behaviour.)

**Bulk sync (`[[autocreate.rules]]`):**

The rule's `params[]` array is rendered per fence and the results become positional args.

```toml
params = ["{{group}}", "{{name}}"]
```

For a fence in group `"Belgium"` named `"Aalst"`, `{0}` = `"Belgium"`, `{1}` = `"Aalst"`.

Whitespace inside a single rendered param splits it into multiple args (`"foo bar"` → two args). Use `"quoted segments"` to keep a multi-word value as one arg.

## Bulk Sync Setup

Define one rule per template-application you want sync to manage. Add to `config.toml`:

```toml
[autocreate]
removal_safety_max_percent = 20

[[autocreate.rules]]
name           = "uk-areas"
guild          = "12345678901234567"
template       = "area"
filter         = "{{eq server \"uk\"}}"
params         = ["{{group}}", "{{name}}"]
remove_missing = false
```

| Field | Required | Description |
|---|---|---|
| `name` | yes | Unique rule identifier (no spaces). Used by `!autocreate sync <name>` to target one rule. |
| `guild` | yes | Discord guild ID where the channels are created. The bot must be a member. |
| `template` | yes | Name of the entry in `channelTemplate.json` to apply. |
| `filter` | no | Optional Handlebars expression evaluated per fence. Empty = match all. |
| `params` | yes | Positional args fed into the template's `{0}`, `{1}`, ... placeholders. |
| `remove_missing` | no | Default `false`. When `true`, fences in cache that aren't in the current geofence list become candidates for orphan removal — but only when the trigger explicitly asks for removals. |

### Filter Expressions

A Handlebars expression evaluated against each fence. The fence's named fields (`name`, `group`, `description`, `color`, `userSelectable`, `displayInMatches`) plus every property from the GeoJSON `properties` object are available as variables.

Truthiness: the rendered string is truthy unless the trimmed value is `""`, `"false"`, or `"0"`. An empty filter matches all.

```handlebars
filter = ""                              # match every fence
filter = "{{beserver}}"                  # match fences with property beserver: true
filter = "{{eq server \"uk\"}}"          # match fences whose property server equals "uk"
filter = "{{and (eq server \"uk\") (gt level 5)}}"
filter = "{{contains tags \"premium\"}}"
```

Available helpers: `eq` `ne` `gt` `lt` `gte` `lte` `and` `or` `not` `contains` — same set as DTS templates.

### removal_safety_max_percent

Top-level guard against catastrophic removals. When a sync is told to remove orphans:

- Only engages when the cache has ≥10 entries (below that, the percentage is too noisy).
- If `(orphan count) / (cache count) > maxPercent`, the removal phase is aborted for that rule. Creates and reuses still run normally.
- `removal_safety_max_percent = 0` disables the safety check entirely.
- Default is 20%. Reasonable for "I changed my filter and now half my channels look orphaned" mistakes.

### remove_missing vs Trigger Removals

Two flags must both be set for a removal to fire:

- `remove_missing = true` on the rule (in `config.toml`) — opt-in at config time
- `removals` keyword on the trigger — opt-in at run time

This is intentional double-confirm: turning `remove_missing` on doesn't suddenly delete things on the next scheduled sync; you still need to explicitly ask.

## Triggering a Sync

### Discord Command

```
!autocreate sync                                   # all rules
!autocreate sync uk-areas                          # one rule
!autocreate sync dryrun                            # preview all, no changes
!autocreate sync uk-areas dryrun                   # preview one
!autocreate sync uk-areas removals                 # actually remove orphans
!autocreate sync uk-areas reset                    # re-apply commands on reused channels
!autocreate sync uk-areas removals force           # bypass the safety threshold
!autocreate sync uk-areas reset removals           # full reconciliation
```

Keywords (`dryrun`, `reset`, `removals`, `force`) are order-independent and can be combined.

Admin-only — inherits the same admin gate as the rest of `!autocreate`.

### HTTP API

```http
POST /api/autocreate/run
Headers: x-poracle-secret: <your-secret>
Content-Type: application/json
```

```json
{
  "rule":     "uk-areas",
  "dry_run":  false,
  "removals": false,
  "force":    false,
  "reset":    false
}
```

Empty `"rule"` runs every rule.

Response:

```json
{
  "status": "ok",
  "rules": [
    {
      "Rule":   "uk-areas",
      "Status": "ok",
      "DryRun": false,
      "Created": ["Aalst", "Antwerp"],
      "Reused":  ["Brussels"],
      "Orphans": [],
      "Removed": [],
      "Skipped": [],
      "Errors":  []
    }
  ]
}
```

`Status` values: `"ok"` (normal), `"busy"` (another sync of this rule already running), `"safety_blocked"` (removal threshold exceeded — re-run with `force` to override).

## What Sync Does, In Order

1. **Lock the rule.** Two concurrent triggers for the same rule → second one returns `Status: "busy"` immediately. Different rules can run in parallel.
2. **Build a guild snapshot.** One call to Discord lists all channels, threads, and roles. Used for everything below.
3. **Reconcile cache against live state.** Cache entries pointing at deleted channels/categories/threads are pruned so the diff loop sees them as missing.
4. **Classify fences:**
    - Fence matches filter, not in cache → `toCreate`
    - Fence matches filter, in cache → `toReuse`
    - Fence in cache, no longer matches filter (or no longer in geofence list) → `orphan`
    - Filter or params render error → `skipped`
5. **Apply create + reuse** for each candidate. Each application:
    - Creates / reuses the category
    - Creates / reuses / moves each channel (if a channel with the right name exists under a different category, it gets moved instead of duplicated)
    - Creates / reuses each thread
    - Posts / refreshes the picker
    - Runs the template's commands (subject to reset semantics — see above)
6. **Remove orphans** if `remove_missing` and `removals` are both on, the safety check passes (or `force`), and there are orphans.
    - Per orphan: delete thread human-rows, delete Poracle webhooks (and their human-rows), delete the channel (Discord cascades the threads automatically).
    - After removals: any cached category that no longer has a child fence is deleted too.
7. **Save the cache** at `config/.cache/autocreate.json`.
8. **Trigger a state reload** so the new channels start receiving alerts immediately.

## Dry-run Mode

`!autocreate sync ... dryrun` (or `"dry_run": true` in the API) runs steps 1-4 normally and reports what 5-7 *would* do without actually doing them. No mutating Discord calls are made; no DB rows are written; no cache file is updated.

Dry-run logs include lines like `>> [dry-run] Would create channel ...`, `>> [dry-run] Would create private thread ...`, `>> [dry-run] Would post N new picker message(s) ...`. Use it to preview a sync against a new rule, verify a filter change, or check what a `removals` run would actually delete.

The summary line marks a dry-run with `(dry run — nothing was changed)`.

## Backups

Every write to a user-editable config file (channel template, DTS templates, `config.toml` during migration) snapshots the previous version into `config/backups/` before overwriting. The backup name is `<source-relative-path>.bak.<YYYY-MM-DD_HHMMSS>`:

- `config/channelTemplate.json` → `config/backups/channelTemplate.json.bak.2026-05-07_113402`
- `config/dts/fort_update.txt` → `config/backups/dts/fort_update.txt.bak.2026-05-07_113402`

Backups are kept for 7 days then auto-removed by a background sweeper. Save anything you want to keep longer somewhere else.

## REST API

For tooling integrations, the bot exposes a small REST surface for reading / writing templates and triggering syncs from outside Discord. See [Auto-create API](../api/autocreate.md) for the capability list and endpoint reference.

## Worked Example

**Goal:** in guild `12345`, create one text channel per Belgium fence under a "Belgium" category, with a private thread for 100% IVs and a picker post.

`config/channelTemplate.json`:

```json
[
  {
    "name": "area",
    "definition": {
      "category": {
        "categoryName": "{0}",
        "roles": [
          { "name": "@everyone", "view": false },
          { "name": "subscribers", "view": true }
        ]
      },
      "channels": [
        {
          "channelName": "{1}",
          "channelType": "text",
          "topic": "Alerts for {1}",
          "controlType": "bot",
          "commands": [
            "area add {1}",
            "track 0 90 great10"
          ],
          "threads": [
            {
              "name": "iv100-{1}",
              "buttonLabel": "100% IVs",
              "buttonStyle": "success",
              "commands": [
                "area add {1}",
                "track 0 100 great5"
              ]
            }
          ],
          "threadPicker": {
            "embedTitle": "{1} alert levels",
            "embedDescription": "Click a button to join the thread for that alert level.",
            "pinned": true
          }
        }
      ]
    }
  }
]
```

`config.toml`:

```toml
[[autocreate.rules]]
name           = "belgium-areas"
guild          = "12345"
template       = "area"
filter         = "{{eq country \"BE\"}}"
params         = ["{{group}}", "{{name}}"]
remove_missing = false
```

Then in Discord:

```
!autocreate sync belgium-areas dryrun
```

Confirms what would happen. Then:

```
!autocreate sync belgium-areas
```

Done. Subsequent runs are idempotent — they reuse existing channels/threads/pickers, only create what's missing. After you add or rename geofences, re-run sync to bring Discord into agreement.

## Troubleshooting

**`I can't find that channel template!`** — `!autocreate <name>` couldn't find a template named `<name>`. Check the `name` field in `channelTemplate.json` matches exactly (case-sensitive, no spaces).

**`No channel templates defined`** — `config/channelTemplate.json` is missing or empty. Create it.

**`No guild has been set`** — `!autocreate` was run from a DM or in a guild that wasn't recognised. Run from the channel you want the new channels created in, or pass an explicit `guild:<id>`.

**`Already syncing`** — another sync of the same rule is in flight. Wait for it to finish.

**`Removal aborted: N of M cached fences would be removed (X%, threshold Y%) — re-run with `force` to override`** — the safety threshold blocked the removal phase. Either:

- The threshold is too tight for legitimate large-scale removal → re-run with `force`.
- Something genuinely went wrong (filter changed in a way you didn't intend) → fix the filter and re-run without `force`.

Creates and reuses already ran successfully; only removals were skipped.

**Threads created, commands didn't run for them** — the bot needs `MANAGE_THREADS` on the parent channel. Check guild permissions.

**Picker buttons don't do anything when clicked** — the bot needs to be online to handle the interaction. If the bot was restarted, button clicks still work (the `custom_id` is stateless) but only after the bot reconnects.

**Channels created but don't receive alerts** — auto-create triggers a state reload after creation, but if you're seeing this immediately, give it 1–2 seconds. If still nothing, check `!tracked` on the channel — if empty, the template's `commands` block didn't apply (look at the auto-create output for errors).

**A re-run of sync recreated channels I already had under a different category** — auto-create now detects channels with the same name across categories and moves them into the canonical category instead of duplicating. If your existing channels are in unexpected categories (e.g. from a previous buggy run), the next sync will consolidate them. Empty leftover categories will need to be deleted by hand.

### Cache Files

Per-rule state lives at `config/.cache/autocreate.json`; picker / thread metadata at `config/.cache/autocreate-threads.json`. Both are safe to delete if you want a clean re-sync — the next run will rebuild them from live Discord state.
