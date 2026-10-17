# Command Reference

Quick reference for all PoracleNG user commands. Discord uses `!` prefix, Telegram uses `/`. Most commands are also available as Discord slash commands — see [Slash Commands](slash.md).

## Filter Syntax — Colon Form

Filter options use `key:value` form: `iv:90`, `iv:90-100`, `cp:2000-3000`, `level:25-35`, `d:500`. Ranges (`low-high`) are preferred over `miniv:`/`maxiv:` pairs. Bare-minimum-with-colon (`iv:99`) is fine when you only need a floor. Legacy no-colon forms (`iv90`, `d500`, etc.) are still accepted.

Keywords and names that contain spaces are typed with underscores or quotes: `no_rsvp`, `mega_legendary`, `slot_changes`, `area:city_centre`, `gym:"Hilltop Gym"`. (Multi-word item, pokemon, move, form and costume names are recognised with plain spaces too.)

Any command followed by `help` (e.g. `!track help`) shows its usage. Several commands can be sent at once — one per line, or separated by `|` (`!track pikachu | track eevee`).

## Registration & Status

| Command | Description |
|---------|-------------|
| `!poracle` | Register with the bot |
| `!unregister` | Remove your registration and all data |
| `!start` | Resume alerts (after `!stop`) |
| `!stop` | Pause all alerts indefinitely |
| `!tracked` | Show all your tracking subscriptions and active mutes. Long lists arrive as a `tracked.txt` file attachment instead of many messages |
| `!tracked area` | Show just your current areas |
| `!script` | Export your current profile's tracking as a list of re-runnable commands (sent as a file when long) |
| `!version` | Show the PoracleNG version, build commit and build date |
| `!help` | Show help index (admin-only topics are hidden from non-admins) |
| `!help <command>` | Show help for a specific command |
| `<command> help` | Show the usage line for a command, e.g. `!raid help` |

## Areas & Locations

See [Areas & Locations](areas.md) for the full surface.

| Command | Description |
|---------|-------------|
| `!area list` | List available areas |
| `!area` | Show your current areas |
| `!area add <name> [<name>...]` | Add one or more areas (`!area <name>...` also adds) |
| `!area remove <name> [<name>...]` | Remove one or more areas |
| `!area show [<name>...\|d<metres>]` | Map of your areas (or the named ones / a distance circle) |
| `!area overview [<name>...]` | All your areas on one map |
| `!location` | Show your default location |
| `!location <lat>,<lon>` | Set default by coords (`51.28, 1.08` with a space also works) |
| `!location <place>` | Set default by address (requires geocoder) |
| `!location remove default` | Clear your default location |
| `!location list` | List default + all saved (named) locations |
| `!location add <name> <coords-or-place>` | Save a named location |
| `!location show <name>` | Show a named location |
| `!location remove <name>` | Delete a named location (refused if any rule references it) |

## Pokemon Tracking

See [Pokemon Tracking](pokemon.md).

| Command | Description |
|---------|-------------|
| `!track <pokemon> [options]` | Track a pokemon |
| `!track everything [options]` | Track all pokemon (filtered) |
| `!untrack <pokemon>` | Stop tracking a pokemon |
| `!untrack id:<N>` | Remove one specific **pokemon** rule by UID |
| `!untrack gen:<n>` / `!untrack <type>` | Remove pokemon rules for a generation / pokemon type |
| `!untrack <pokemon> costume:<name>` | Remove only the rules for that costume |
| `!untrack everything` | Remove all pokemon tracking |
| `!untrack <raid\|egg\|quest\|invasion\|lure\|nest\|gym\|fort\|maxbattle> ...` | Same as `!<type> remove ...`, e.g. `!untrack raid id:12` |

### Pokemon Filter Options

| Option | Description | Example |
|--------|-------------|---------|
| `iv:<n>` / `iv:<low>-<high>` | IV % (min or range) | `iv:90`, `iv:90-100` |
| `cp:<n>` / `cp:<low>-<high>` | CP (min or range) | `cp:2000-3000` |
| `level:<n>` / `level:<low>-<high>` | Pokemon level | `level:30-35` |
| `atk:<n>`, `def:<n>`, `sta:<n>` | Individual IVs (0-15, min or range) | `atk:13-15` |
| `d:<n>` | Distance in metres | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `t:<n>` | Min time remaining (sec) | `t:300` |
| `form:<name>` | Form name | `form:alolan` |
| `costume:<name\|id>` | Costume (`costume:0` = no costume) | `costume:0` |
| `gen:<n>` | Generation (narrows `everything` or named pokemon) | `everything gen:1` |
| `rarity:<n>` / `rarity:<low>-<high>` | Rarity tier (1-6); a single value is an exact match | `rarity:3-5` |
| `size:<n>` / `size:<low>-<high>` | Size (1-5 or `xxs`/`xs`/`m`/`xl`/`xxl`); a single value is an exact match | `size:xxl` |
| `great:<n>`, `ultra:<n>`, `little:<n>` | PVP league rank | `great:1-10` |
| `mega`, `mega:x`, `mega:y` | Rank as Mega Evolution (with a league filter) | `great:5 mega` |
| `greatcp:<n>`, `ultracp:<n>`, `littlecp:<n>` | League min CP | `greatcp:1400` |
| `cap:<n>` | PVP level cap | `cap:50` |
| `male`, `female`, `genderless` | Gender filter | `male` |
| `template:<n>` | Template number | `template:2` |
| `clean` | Auto-delete expired | `clean` |
| `edit` | Edit the alert in place when the pokemon changes (5.3+) | `edit` |
| `everything` | All pokemon | `everything` |
| `<type>` | Pokemon type — only narrows `everything` or named pokemon | `everything dragon` |
| `individually` | Track as individual rows | `individually` |

## Raid & Egg Tracking

See [Raids & Eggs](raids.md).

| Command | Description |
|---------|-------------|
| `!raid <pokemon\|level:n> [options]` | Track raids |
| `!raid remove <target>` | Remove raid tracking |
| `!egg level:<n> [options]` | Track eggs |
| `!egg remove <level>` | Remove egg tracking |

Common options: `level:<n>`, level names (`legendary`, `mega`, `mega_legendary`, `elite`, `primal`, `shadow`, `shadow_legendary`, …), `ex`, `d:<n>`, team (`instinct`/`valor`/`mystic`/`harmony` or `yellow`/`red`/`blue`/`gray` — bare words, no `team:` prefix), `gym:<name>`, `move:<name>`, `form:<name>`, `costume:<name>`, `area:<name>`, `location:<name>`, `template:<n>`, `clean`, `edit`, `rsvp`, `rsvp_only`, `no_rsvp`, `everything`, `remove`. Pokemon types and `gen:` are not raid filters.

## MAX Battle Tracking

See [MAX Battle Tracking](maxbattle.md).

| Command | Description |
|---------|-------------|
| `!maxbattle level:<n> [options]` | Track MAX Battles by level (1-5) |
| `!maxbattle <pokemon> [options]` | Track MAX Battles for a specific pokemon |
| `!maxbattle everything [options]` | Track all MAX Battle levels |
| `!maxbattle remove <target>` | Remove MAX Battle tracking |

Common options: `level:<n>`, `gmax`, `d:<n>`, `move:<name>`, `form:<name>`, `area:<name>`, `location:<name>`, `template:<n>`, `clean`. `gen:<n>` and pokemon types only narrow a list of named pokemon.

## Other Tracking

| Command | Description |
|---------|-------------|
| `!quest <reward> [options]` | Track quest rewards (see [Quests](quests.md)) |
| `!incident <type> [options]` | Track invasions / Kecleon / Gold Pokéstops / Showcases (see [Invasions](invasions.md)) |
| `!lure <type> [options]` | Track lures (see [Lures](lures.md)) |
| `!nest <pokemon> [options]` | Track nests (see [Nests](nests.md)) |
| `!gym <team> [options]` | Track gym changes (see [Gyms](gyms.md)) |
| `!fort [options]` | Track fort updates (new / removed / renamed stops and gyms — see [Gyms](gyms.md)) |
| `!weather` | Not available yet — replies that weather tracking isn't supported |

Each tracker supports the common filter set: `d:<n>`, `location:<name>`, `area:<name>`, `template:<n>`, `everything`, `remove`. `clean` is accepted by every tracker except `!fort`. `edit` is accepted only by `!track`, `!raid`, `!egg` and `!lure`.

## Profiles

See [Profiles](profiles.md).

| Command | Description |
|---------|-------------|
| `!profile list` | List your profiles |
| `!profile add <name>` | Create a new profile |
| `!profile <n\|name>` (or `switch <n\|name>`) | Switch profile (must already exist) |
| `!profile remove <n\|name>` (or `delete`) | Delete a profile (not profile 1) |
| `!profile copyto <name...\|all>` | Replace the tracking on other profiles with a copy of the current one |
| `!profile settime <times>` (or `hours`) | Set auto-switch schedule (e.g. `weekday09:00 weekend10:00`, `weekday:9-17/2`) |
| `!profile cleartime` | Remove the current profile's schedule |

## Mute

See [Muting Alerts](mute.md).

| Command | Description |
|---------|-------------|
| `!mute <scope> <value> [duration:<d>]` | Mute a gym / pokemon / area / pokestop / station |
| `!mute id:<N> [duration:<d>]` | Mute one tracking rule |
| `!mute everything [duration:<d>]` | Mute ALL alerts |
| `!mute <pokemon> [duration:<d>]` | Shorthand — pokemon scope assumed |
| `!unmute <scope> <value>` | End a mute early |
| `!unmute everything` (or `all`) | Clear all active mutes |

Default duration is 1h if omitted; durations look like `30m`, `1h30m` or `2d`. Mutes are lost on restart. Per-type aliases only work for `!<type> mute id:<N>`, `!track mute <pokemon>` and `!gym mute <gym>` — see [Muting Alerts](mute.md#per-type-aliases).

## Alert Summaries

Buffer matched events and deliver them grouped on a schedule. See [Alert Summaries](summaries.md).

| Command | Description |
|---------|-------------|
| `!quest <reward> summary` | Opt a quest tracking rule into summary delivery |
| `!summary quest` | Show current schedule and buffer count |
| `!summary quest settime <times>` | Set delivery schedule (e.g. `weekday07:30 weekend10:00`, `every08:00`) |
| `!summary quest cleartime` | Remove the schedule (events still buffer) |
| `!summary quest now` | Flush the buffer immediately |

## Info & Lookups

See [Info & Lookups](info.md).

| Command | Description |
|---------|-------------|
| `!info <pokemon>` | Pokemon details (types, stats, evolutions, 100% CP) |
| `!info <pokemon> forms` / `costumes` | Forms or costumes for one pokemon |
| `!info costumes` | Recently seen costumes |
| `!info moves` | List moves + IDs |
| `!info items` | List items the bot recognises (canonical `!quest` tokens) |
| `!info weather [<lat>,<lon>]` | In-game weather at your location (or the given coordinates) + boosted types |
| `!info templates` | DTS template numbers loaded on this server |
| `!info rarity` | Pokemon rarity tier breakdown |
| `!info shiny` | Observed shiny rates per species |

## Slash Commands

If your operator has enabled them, all the above (except admin-only ones) are also available as Discord `/track`, `/quest`, etc. with autocomplete and option panels. See [Slash Commands](slash.md).

## Language

| Command | Description |
|---------|-------------|
| `!language <code>` | Switch language (e.g. `en`, `de`, `fr`) |

## Natural Language

| Command | Description |
|---------|-------------|
| `!ask <question>` | Describe what you want to track in plain English; the bot suggests the equivalent tracking command |

`!ask` is available only when the operator has enabled it in `config.toml`:

```toml
[ai]
enabled = true
```

Example:

```
!ask track any shiny pokemon within 2km of me
```

The bot replies with a suggested `!track` command you can copy, edit, and send — it doesn't run the command for you. Operators can also enable `suggest_on_dm` in `[ai]` to have unrecognised DMs silently parsed as `!ask` queries.

## Common Options

These options are available across most tracking commands:

| Option | Description |
|--------|-------------|
| `d:<n>` | Distance from your location in metres |
| `location:<name>` | Distance from a named (saved) location (requires `d:`) |
| `area:<name>[,<name>]` | Restrict the rule to specific areas |
| `template:<n>` | Alert template number (see `!info templates`) |
| `clean` | Auto-delete expired alerts (not `!fort`) |
| `edit` | Edit the alert in place on updates (`!track`, `!raid`, `!egg`, `!lure` only) |
| `remove` | Remove (instead of add) tracking |
| `everything` | All items of that type |

`d:` and `area:` are mutually exclusive. `location:` requires `d:`. See [Areas & Locations](areas.md#per-rule-overrides) for the full rules.

## Testing

`!poracle-test` (Telegram `/poracle-test`) is an **admin-only** command for sending sample alerts — see [Admin Commands](../operation/admincommands.md).

## Discord-Specific

| Command | Description |
|---------|-------------|
| `!role list` | List available roles |
| `!role add <name>` | Add a role |
| `!role remove <name>` | Remove a role |
| `!role membership` | Show your roles |
