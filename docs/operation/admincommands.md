# Admin Commands

Admin commands are available to users listed in `admins` under `[discord]` or `[telegram]` in `config.toml`. Non-admins don't see them in `!help`, and most refuse with a 🙅 react.

Delegated admins (`[[discord.delegated_admins]]`, `[[telegram.delegated_admins]]`, `[[discord.webhook_admins]]`, `user_tracking_admins`) are **not** full admins: they can manage tracking for the channels, webhooks or users they've been given (see [Tracking on behalf of others](#tracking-on-behalf-of-others)), but can't run the commands on this page. The exception is Telegram community admins from [area security](../configuration/areasecurity.md), who can run `/channel add`/`remove` and, from 5.3, a `/userlist` limited to their communities.

Telegram uses the same commands with a `/` prefix (`/userlist`, `/pa status`, …) unless noted as Discord-only.

!!! tip "REST API"
    Most admin surfaces — user/tracking/profile CRUD, auto-create, DTS editing, named locations — are also available over HTTP. See the [API index](../api/index.md) for a summary and [`API.md`](https://github.com/jfberry/PoracleNG/blob/main/API.md) in the PoracleNG repo for the canonical endpoint reference.

## Poracle Admin (`!poracle-admin` / `!pa`)

`!poracle-admin` (short alias `!pa`) is the umbrella for live operations: reloading files, checking health, inspecting rate limits, pausing delivery, managing slash commands and more, all without restarting the processor.

```
!pa                          # list the subgroups
!pa <group>                  # show that group's subcommands
!pa <group> <subcommand> …   # run it
```

It's admin-only. A non-admin gets a text reply ("This command is reserved for administrators.") rather than a silent react. It's text-only, with no slash-command version. It works on Telegram as `/pa …`, but the Discord-specific parts (`slash`, `reconcile`, `emoji upload`/`discord-config`) reply that they're not available there.

!!! note "Replaces `!info poracle` and `!info config`"
    `!info poracle` and `!info config` now just reply with a pointer to `!pa status` and `!pa config`.

### `slash` — Discord slash-command registration

| Command | What it does |
|---|---|
| `!pa slash sync` | Push the current slash-command set to Discord. The push is cached by fingerprint, so nothing is sent if the commands haven't changed. |
| `!pa slash force-resync` | Clear the fingerprint cache and push unconditionally. Use this when clients show stale or wrong commands. |
| `!pa slash clear-global` | Remove all globally registered slash commands (e.g. after switching from global to per-guild registration). |
| `!pa slash clear-guild <guild_id>` | Remove the slash commands registered in one guild. |
| `!pa slash status` | Show the last sync time and fingerprint for each scope (global and each guild). |
| `!pa slash list` | List the registered commands as clickable mentions, plus copyable `</name:id>` syntax for pasting into channel topics or pinned posts. **5.3+** |

These need slash commands to be enabled (`[discord.slash_commands] enabled = true`). See [Slash Commands](../userguide/slash.md).

### `reload` — reload from disk or database

| Command | What it does |
|---|---|
| `!pa reload dts` | Reload DTS templates and partials from disk. |
| `!pa reload geofence` | Reload geofence files (and Koji) and refresh state. |
| `!pa reload state` | Reload tracking rules and humans from MySQL immediately, without the normal debounce. |

Each reply reports how long the reload took and what was loaded (for example the number of templates, fences or tracking rules).

### `status` — health snapshot

| Command | What it does |
|---|---|
| `!pa status` | One-screen health view covering: version and uptime, webhook arrival rate (per minute over the last 5/15/60 minutes), delivery in-flight and queue depth, Discord and Telegram rate-limit state and recent 429s, alert-limit breaches and bans, the summary buffer, tracking-rule and human counts, and the MySQL ping and connection pool. |
| `!pa status -v` | Verbose mode. Adds per-route Discord rate-limit detail and the full per-type webhook breakdown. |

When delivery is paused (see `maintenance` below), the status starts with a 🔴 PAUSED banner.

### `maintenance` — pause and resume delivery

| Command | What it does |
|---|---|
| `!pa maintenance pause [reason…]` | Stop outbound delivery to Discord, Telegram and webhooks. Alias: `start`. |
| `!pa maintenance resume` | Resume delivery. Alias: `stop`. |
| `!pa maintenance status` | Show whether delivery is running or paused, the reason, and for how long. |

!!! warning "Paused alerts are dropped, not queued"
    During a pause, webhooks are still received, matched and rendered, but the resulting alerts are **thrown away**. Nothing is held back to send on resume, so you don't get a flood of stale alerts. Rate-limit notices still go out. From 5.3 the `!pa maintenance` replies say so; in 5.2.x they wrongly said alerts were queued and would drain on resume.

    While paused, every command reply on both platforms ends with "🔧 Maintenance mode is active — alerts are not being delivered." so users know why alerts have stopped. The pause is in memory only: restarting the processor resumes delivery.

### `ratelimit` — alert-limit inspection

| Command | What it does |
|---|---|
| `!pa ratelimit list` | All destinations currently over their limit or banned, for both the alert and summary buckets. |
| `!pa ratelimit show <target>` | Counters, window, 24-hour violation count and ban expiry for one destination. |
| `!pa ratelimit reset <target>` | Clear the counters and violation history for a destination. |
| `!pa ratelimit userlist` | Shortcut for `!userlist disabled`. |

`<target>` is either the form shown by `list` (e.g. `discord:user/123456789`) or a bare ID. `reset` does **not** re-enable a user who was auto-disabled for repeated breaches. Use `!enable <id>` for that. See [Rate Limits](ratelimits.md).

### `summary` — summary buffer

| Command | What it does |
|---|---|
| `!pa summary list` | Every user with entries buffered for a [summary](../userguide/summaries.md), with entry counts and the next scheduled send. |
| `!pa summary show <id>` | One user's buffered entries, grouped by reward. |
| `!pa summary fire <id> [alertType]` | Send that user's summary now. `alertType` defaults to `quest`, which is currently the only summary type. |

### `config` — view the running config

| Command | What it does |
|---|---|
| `!pa config` | The whole effective config (after overrides), with secrets redacted. |
| `!pa config keys` | Section names and how many keys each has. |
| `!pa config <section>` | Just one section, e.g. `!pa config discord`. |
| `!pa config search <term>` | Find every key whose path or value contains `<term>`, e.g. `!pa config search shlink`. Alias: `find`. Redacted values are searched as `***`, so a search can't reveal a secret. |
| `!pa config help` | List these options. |

From 5.3, section names and search terms ignore case and treat `_` and spaces alike, so `!pa config alert_limits`, `!pa config webhookLogging` and `!pa config search dm_limit` work as typed. In 5.2.x, unquoted underscores were turned into spaces and the section lookup was case-sensitive, so you had to quote: `!pa config "alert_limits"`.

!!! note "5.3+"
    From 5.3, config output is shown in code blocks, so TOML values keep their formatting and aren't mangled by Discord markdown.

### `warnings` — recent warnings and errors

The processor keeps WARN and ERROR log lines in memory, so you can see what went wrong without reading the log file.

| Command | What it does |
|---|---|
| `!pa warnings` | Both buffers: startup, then recent. |
| `!pa warnings startup` | Warnings logged during startup (up to 200 entries; fixed once startup finishes). |
| `!pa warnings recent` | The last 50 warnings and errors since startup. |
| `!pa warnings clear` | Empty the recent buffer. The startup buffer can't be cleared. |

### `cache` — geocoder cache

| Command | What it does |
|---|---|
| `!pa cache stats` | Geocoder cache entries (memory and disk), hits, misses and hit rate. |
| `!pa cache clear geocoder` | Clear the in-memory geocoder cache. The disk cache is left alone. |

### `emoji` — emoji configuration

| Command | What it does |
|---|---|
| `!pa emoji list` | Every emoji key, with its value on each platform. |
| `!pa emoji reload` | Reload `config/emoji.json` from disk. |
| `!pa emoji test <key>` | Show what one emoji key resolves to on the current platform. |
| `!pa emoji upload [overwrite]` | Discord only. Upload emojis from your uicons `img_url` repository to the current guild and reply with a generated `emoji.json`. With `overwrite`, existing emojis are deleted and re-uploaded. Same as `!poracle-emoji upload`. |
| `!pa emoji discord-config` | Discord only. Generate `emoji.json` from the guild's existing emojis without uploading anything. |

### `reconcile` — Discord role sync

| Command | What it does |
|---|---|
| `!pa reconcile run` | Run the full Discord role reconciliation now instead of waiting for the next `check_role_interval`. Replies when it finishes. |
| `!pa reconcile user <discord_id>` | Re-check a single user's roles and registration. |

Both need `[discord] check_role = true`. See [Discord Reconciliation](../config/reconciliation-discord.md).

## Registering Channels, Groups and Webhooks

Registered channels, groups and webhooks receive alerts the same way a user does. Each one needs its own areas or location, and its own tracking.

### Discord: `!channel`

`!channel add` registers **the channel you type it in**. It can't register a channel by mention.

```
!channel add                              # register this channel (or thread)
!channel add area:downtown language:de    # …with an initial area and language
!channel remove                           # unregister this channel and delete its tracking
!channel add name:<name> <webhook_url>    # register a Discord webhook by URL
!channel remove name:<name>               # unregister a webhook by name
```

- Run in a thread, `!channel add` registers the thread (type `discord:thread`).
- `area:` takes one area name and `language:` takes a language code. Both are optional. From 5.3, area names with underscores work unquoted (`area:gent_centrum`), and a known area is stored under the fence's own name. In 5.2.x an `area:` or `name:` value containing an underscore was silently dropped.
- Adding a webhook needs both `name:` and the URL. Webhooks can be added from a DM; channels can't.
- `!channel` is for full admins only.

### Discord: `!webhook`

`!webhook` manages Discord webhooks on the current channel. Run it in the guild channel, not in a DM. URLs are sent to your DMs.

```
!webhook list                 # DM you every webhook on this channel, with URLs
!webhook create [name:<n>]    # create a webhook (default name "Poracle") and DM you the URL
!webhook add [name:<n>]       # create or reuse this channel's "Poracle" webhook and register it as a target
!webhook remove [name:<n>]    # unregister that webhook target and delete its tracking
```

For `add` and `remove`, the target's name defaults to the channel name. Webhook target names can't contain underscores. `add` and `remove` refuse to run in a channel that is already registered with `!channel add`; run `!channel remove` first.

### Telegram: `/channel`

```
/channel add                               # register this group, or this forum topic
/channel remove                            # unregister it and delete its tracking
/channel add name:<name> <channel_id>      # register a Telegram channel by ID (full admins only)
/channel remove name:<name> <channel_id>
```

- **Groups**: run `/channel add` in the group (type `telegram:group`).
- **Forum topics**: run `/channel add` inside a topic to register **just that topic** (type `telegram:topic`, ID `<chatId>:<topicId>`). Each topic is a separate target with its own tracking, and commands typed in a topic apply to that topic.
- **Channels**: Telegram channels can't receive commands, so register them from elsewhere with `name:` and the numeric channel ID (find the ID with `/identify`; see below).
- Community admins (from [area security](../configuration/areasecurity.md)) can also register groups. The group gets that community's membership and area restriction.

## Tracking on Behalf of Others

Mentioning a user or channel doesn't change who a command applies to. Use one of these instead:

| How | Applies to | Who can do it |
|---|---|---|
| Run the command **inside a registered channel/group/topic** | That channel | Admins; delegated admins for that channel, category or guild (`[[discord.delegated_admins]]`) or chat (`[[telegram.delegated_admins]]`); and, on Telegram with area security, community admins for groups in their communities. Category and Telegram delegation are 5.3+. |
| Add `user:<id>` | That registered user | Admins and `user_tracking_admins` |
| Add `name:<name>` | The webhook, channel or group with that registered name | Admins, and `[[discord.webhook_admins]]` for that webhook name |

=== "Discord"

    ```
    !track pikachu iv100                       # typed in #alerts-pokemon → tracks for that channel
    !track pikachu iv100 user:123456789012345678
    !area add downtown name:city-webhook
    !tracked name:alerts-pokemon
    ```

=== "Telegram"

    ```
    /track pikachu iv100 user:123456789
    /area add downtown name:MyGroup
    ```

The bot replies "This command is being executed as …" so you can see which target was used. `name:` matches webhooks, Discord channels, Telegram channels and Telegram groups by their registered name. Put names containing underscores in double quotes (`"name:iv_100"`), because underscores in unquoted arguments become spaces.

## User Management

### `!userlist`

Lists registered users, channels, groups and webhooks, with their areas and a status marker (🛑 stopped, ⛔ disabled). From 5.3, Telegram community admins can run it too, and see only humans in the communities they administer. Add filters to narrow the list:

```
!userlist
!userlist disabled                 # enabled | disabled
!userlist discord user             # discord | telegram  and  user | channel | group | webhook
!userlist telegram group enabled
```

### `!disable` / `!enable`

```
!disable <id> [<id> …]            # admin-disable one or more targets
!enable <id> [<id> …]             # re-enable them
```

Targets can be IDs, Discord mentions, or webhook/channel names. `!disable` stops alerts for the target until an admin enables it again. `!enable` also clears a user's own `!stop` (including a rate-limit stop) and resets the failed-delivery count.

From 5.3:

- IDs with no registration are listed as "Not registered: …" instead of being reported as done. If nothing was applied, the reply reacts 🙅. In 5.2.x the command reported success for unknown IDs.
- `!enable` (and `!start`) also clear the target's rate-limit breach history, so a re-enabled user who floods again is banned again. See [Rate Limits](ratelimits.md).

### `!unregister`

```
!unregister <id> [<id> …]         # admin: remove these users and all their tracking
!unregister                       # non-admin: remove yourself
```

An admin can't unregister themselves this way. The command refuses if the only target is you. From 5.3, unknown IDs are listed as "Not registered" rather than reported as removed.

## Communities

`!community` manages community membership when [area security](../configuration/areasecurity.md) is enabled. Full admins only.

```
!community list                              # all configured communities
!community                                   # show the current target's communities
!community show [<id> …]
!community add <community> <id> [<id> …]
!community remove <community> <id> [<id> …]
!community clear <id> [<id> …]               # remove from all communities
```

Run in a registered channel, `add`, `remove` and `clear` apply to that channel when you don't list any IDs. In a DM they won't apply to you by accident: you have to give your own ID, and the bot suggests the exact command. Changing communities also recalculates the target's area restriction.

## `!apply` — run commands as other targets

```
!apply <targets> | <command> <args> | <command> <args> …
```

This runs each command as each target. Targets are IDs or registered names of webhooks, channels or groups. Names are matched with SQL `LIKE`, so `%` works as a wildcard. Command names are the English ones without the prefix.

```
!apply 123456789012345678 alerts-% | track pikachu iv100 | raid level5
```

The reply shows `>>> Executing as <type> / <name>` for each target, then the result of each command.

## `!broadcast`

`!broadcast` sends a pre-written message from `config/broadcast.json` to registered **users** (not channels or webhooks) by location or area.

```
!broadcast [<lat>,<lon> d<metres>] [@<area> …] [test] <messageId>
```

```
!broadcast @downtown @uptown donate          # users with either area
!broadcast 51.50,-0.12 d5000 donate          # users whose location is within 5 km
!broadcast test donate                       # send only to yourself, as a preview
```

- You need a location with a distance, at least one `@area`, or `test`. A location and areas together are combined with OR.
- Disabled, stopped and admin-disabled users are skipped. The reply lists who was sent the message.
- If any recipient's platform has no version of the message, nothing is sent.

`config/broadcast.json` is a list of messages. Each one has an `id`, an optional `platform` (`discord` or `telegram`; leave it out to use the message on both), and a `template` in Discord or Telegram message format:

```json
[
  {
    "id": "donate",
    "platform": "discord",
    "template": { "embed": { "title": "Donations needed", "description": "More donations are needed for this area" } }
  },
  {
    "id": "donate",
    "platform": "telegram",
    "template": { "content": "More donations are needed for this area" }
  },
  {
    "id": "2",
    "template": { "content": "This service takes a substantial amount of effort to maintain, please consider donating" }
  }
]
```

!!! warning
    The file must be plain JSON: `//` comments make `!broadcast` reply "Failed to parse broadcast.json". Extra fields are ignored, so use a `"_comment": "…"` field for notes. From 5.3 the repository's `examples/broadcast.json` is valid JSON and can be copied as is; the 5.2.x copy has `//` comments that you must remove.

## Backup & Restore

`!backup` saves **one target's tracking** (the current target, current profile) to a file on the server. `!restore` loads it onto another target. Use them to copy a standard tracking set between channels.

```
!backup <name>             # save to backups/<name>.json
!backup list               # list saved backups
!backup remove <name>      # delete one
!restore <name>            # replace the current target's tracking with the backup
!restore list
```

- Names can contain only letters, numbers, `-` and `_`. Quote names that contain underscores.
- Backups are saved in `backups/` under the processor base directory. Nothing is attached to the chat.
- Combine with in-channel targeting, `user:` or `name:` to back up or restore a particular channel, user or webhook.
- Backups cover every tracking type: pokemon, raids, eggs, quests, invasions, lures, nests, gyms, weather, max battles and fort updates. Profiles and summary schedules aren't included; a backup is a per-profile tracking template.

!!! info "PoracleNG 5.3+: `!restore` replaces everything"
    From 5.3, `!restore` clears **every** tracking type on the target's current profile and refills it from the file, in one transaction. A type that is empty or missing from the backup ends up empty, and if anything fails nothing changes. The file is validated first: an unknown table or column rejects the whole restore before anything is touched.

    In 5.2.x, `!restore` only replaced the types that had rows in the backup, kept other types, and didn't validate table or column names.

## Exporting Tracking (`!script`)

`!script` isn't admin-only. Anyone can run it to get their current profile's tracking as a list of commands that would recreate it. The list is sent as `tracking_script.txt` if it's too long for one message:

```
!script
```

The export covers pokemon, raids, eggs, invasions, lures, nests, gyms, quests and, from 5.3, max battles and fort updates (5.2.x leaves those two out). Admins can add `user:<id>` or `name:<name>` to export someone else's tracking.

## Finding IDs

=== "Discord"

    Discord has no identify command. Enable **Developer Mode** (User Settings → Advanced), then right-click a user, channel, role or server and choose **Copy ID**.

    `!poracle-id` (admin) replies with an `id.txt` file listing every custom emoji and role in the guild with its ID, in a format you can paste into `emoji.json` or role config. Run it in a guild channel, or add `guild<id>` when running it from a DM.

=== "Telegram"

    `/identify` works for anyone, with no registration needed, in a private chat, group, forum topic or channel:

    - private chat: your user ID
    - group: the group ID and your user ID
    - forum topic: the group ID, topic ID and your user ID
    - channel post: the channel ID

## Discord Utilities

### `!poracle-clean`

```
!poracle-clean
```

Deletes the bot's own messages from the last 100 in the current channel. Admins can use it anywhere; other users only in their DM with the bot.

### `!poracle-emoji`

```
!poracle-emoji                    # generate emoji.json from the guild's current emojis
!poracle-emoji upload             # upload missing type/weather/team/etc. emojis from your uicons img_url
!poracle-emoji upload overwrite   # delete and re-upload existing ones
```

Admin-only. The reply includes a ready-made `emoji.json`. `img_url` must be a uicons repository. From a DM, add `guild<id>`. `!pa emoji upload` / `discord-config` do the same job.

### `!role`

Self-service role assignment, configured with `[[discord.role_subscriptions]]` (see [Discord Admin Operations](discord.md#role-subscriptions)).

```
!role list                  # roles you can pick (☑️ = you have it)
!role membership            # only the roles you have
!role add <role> [<role> …]
!role remove <role> [<role> …]
```

Users run `!role` in a DM with the bot. Admins can also use it in channels and add `user:<id>` to change someone else's roles.

## Auto-create Channels

Build Discord channels (and optional categories, threads, and click-to-join pickers) from a template, either one area at a time or in bulk across every matching geofence.

=== "Discord"

    ```
    !autocreate <template> <args...>            # Interactive: create one set of channels
    !autocreate sync                            # Bulk: run all configured rules
    !autocreate sync <rule>                     # Run one rule by name
    !autocreate sync <rule> dryrun              # Preview without changing anything
    !autocreate sync <rule> reset               # Re-apply commands on reused channels
    !autocreate sync <rule> removals            # Delete orphaned channels
    !autocreate sync <rule> removals force      # Bypass the safety threshold
    ```

See [Channel Auto-create](../configuration/autocreate.md) for the template format, rule configuration, and exactly what each keyword does. The same operations are available over HTTP via the [Auto-create API](../api/autocreate.md).

## Testing Alerts (`!poracle-test`)

`!poracle-test` sends a test alert, built from sample webhook data, to you (or the current target), so you can check templates and config. Admins only. On Telegram it's `/poracle-test`.

```
!poracle-test <type>                                   # list the test IDs for a type
!poracle-test <type> <testId> [template:N] [language:xx]
```

```
!poracle-test pokemon
!poracle-test pokemon hundo
!poracle-test raid level5 template:2
!poracle-test quest quest-pokecoins language:de
!poracle-test nest riverside                # 5.3+
```

| Type | Test IDs |
|---|---|
| `pokemon` | `boring`, `costume`, `hundo`, `great-rank9`, `great-rank1`, `ultra1`, `unencountered`, `boosted`, `shiny` |
| `raid` | `egg1`, `level1`, `egg5`, `level5`, `egg6`, `level3` |
| `pokestop` | `invasion`, `lure`, `giovanni`, `goldlure`, `showcase` |
| `incident` | `kecleon`, `goldstop`, `pokemoncontest` |
| `showcase` | `type`, `buddy` |
| `quest` | `quest-item`, `quest-stardust`, `quest-pokecoins`, `quest-pokemon`, `quest-energy` |
| `quest-summary` | `stardust` |
| `gym` | `teamchange` |
| `max-battle` | `level1`, `level3` |
| `fort-update` | `edit`, `editdesc`, `editloc`, `editdescimg`, `editall`, `new`, `remove` |
| `monster-changed` | `ditto-reveal` |
| `rsvp-changes` | `level5` |
| `weatherchange` | `rain` |
| `nest` | `riverside` (5.3+; 5.2.x bundles no nest data, so add your own) |

DTS template type names also work as the type: `monster`, `egg`, `invasion`, `lure`, `maxbattle`, `monsterchanged` and so on. `!poracle-test` with no arguments lists the accepted types.

- `template:` picks a DTS template ID. If that template doesn't exist, the test is refused.
- `language:` renders in another language.
- Test data comes from the bundled `fallbacks/testdata.json`, merged with your own `config/testdata.json`. An entry with the same type and test ID in your file replaces the bundled one, and new entries are added. Timestamps are moved to the present, so test alerts don't show as already expired.
