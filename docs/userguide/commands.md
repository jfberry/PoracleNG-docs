# Command Reference

Quick reference for all PoracleNG user commands. Discord uses `!` prefix, Telegram uses `/`. Most commands are also available as Discord slash commands — see [Slash Commands](slash.md).

## Filter Syntax — Colon Form

Filter options use `key:value` form: `iv:90`, `iv:90-100`, `cp:2000-3000`, `level:25-35`, `d:500`, `team:valor`. Ranges (`low-high`) are preferred over `miniv:`/`maxiv:` pairs. Bare-minimum-with-colon (`iv:99`) is fine when you only need a floor. Legacy no-colon forms (`iv90`, `d500`, etc.) are still accepted.

## Registration & Status

| Command | Description |
|---------|-------------|
| `!poracle` | Register with the bot |
| `!unregister` | Remove your registration and all data |
| `!start` | Resume alerts (after `!stop`) |
| `!stop` | Pause all alerts indefinitely |
| `!tracked` | Show all your tracking subscriptions and active mutes |
| `!version` | Show bot version |
| `!help` | Show help index |
| `!help <command>` | Show help for a specific command |

## Areas & Locations

See [Areas & Locations](areas.md) for the full surface.

| Command | Description |
|---------|-------------|
| `!area list` | List available areas |
| `!area` | Show your current areas |
| `!area add <name> [<name>...]` | Add one or more areas |
| `!area remove <name>` | Remove an area |
| `!area clear` | Remove all areas |
| `!location` | Show your default location |
| `!location <lat>,<lon>` | Set default by coords |
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
| `!untrack id:<N>` | Remove one specific rule by UID |
| `!untrack everything` | Remove all pokemon tracking |

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
| `gen:<n>` | Generation | `gen:1` |
| `rarity:<n>` / `rarity:<low>-<high>` | Rarity tier (1-5) | `rarity:3-5` |
| `size:<n>` / `size:<low>-<high>` | Size category (1-5) | `size:5` |
| `weight:<low>-<high>` | Weight range | `weight:0-1` |
| `great:<n>`, `ultra:<n>`, `little:<n>` | PVP league rank | `great:1-10` |
| `greatcp:<n>`, `ultracp:<n>`, `littlecp:<n>` | League min CP | `greatcp:1400` |
| `cap:<n>` | PVP level cap | `cap:50` |
| `male`, `female`, `genderless` | Gender filter | `male` |
| `template:<n>` | Template number | `template:2` |
| `clean` | Auto-delete expired | `clean` |
| `edit` | Single message, edited in place | `edit` |
| `everything` | All pokemon | `everything` |
| `individually` | Track as individual rows | `individually` |

## Raid & Egg Tracking

See [Raids & Eggs](raids.md).

| Command | Description |
|---------|-------------|
| `!raid <pokemon\|level:n> [options]` | Track raids |
| `!raid remove <target>` | Remove raid tracking |
| `!egg level:<n> [options]` | Track eggs |
| `!egg remove <level>` | Remove egg tracking |

Common options: `level:<n>`, `ex`, `d:<n>`, `team:instinct`/`valor`/`mystic`/`harmony`, `gym:<name>`, `move:<name>`, `area:<name>`, `location:<name>`, `template:<n>`, `clean`, `edit`, `rsvp`, `rsvp_only`, `everything`, `remove`.

## MAX Battle Tracking

See [MAX Battle Tracking](maxbattle.md).

| Command | Description |
|---------|-------------|
| `!maxbattle level:<n> [options]` | Track MAX Battles by level (1-5) |
| `!maxbattle <pokemon> [options]` | Track MAX Battles for a specific pokemon |
| `!maxbattle everything [options]` | Track all MAX Battle levels |
| `!maxbattle remove <target>` | Remove MAX Battle tracking |

Common options: `level:<n>`, `gmax`, `d:<n>`, `move:<name>`, `gen:<n>`, `form:<name>`, `area:<name>`, `location:<name>`, `template:<n>`, `clean`.

## Other Tracking

| Command | Description |
|---------|-------------|
| `!quest <reward> [options]` | Track quest rewards (see [Quests](quests.md)) |
| `!incident <type> [options]` | Track invasions / Kecleon / Gold Pokéstops / Showcases (see [Invasions](invasions.md)) |
| `!lure <type> [options]` | Track lures (see [Lures](lures.md)) |
| `!nest <pokemon> [options]` | Track nests (see [Nests](nests.md)) |
| `!gym <team> [options]` | Track gym changes (see [Gyms](gyms.md)) |
| `!fort [options]` | Track fort updates (new / removed / renamed stops and gyms) |
| `!weather` | Track weather changes |

Each tracker supports the common filter set: `d:<n>`, `location:<name>`, `area:<name>`, `template:<n>`, `clean`, `everything`, `remove`.

## Profiles

See [Profiles](profiles.md).

| Command | Description |
|---------|-------------|
| `!profile list` | List your profiles |
| `!profile <n>` | Switch to profile N |
| `!profile name <name>` | Name current profile |
| `!profile copy <n>` | Copy current to profile N |
| `!profile delete <n>` | Delete profile N |
| `!profile settime <times>` | Set auto-switch schedule (e.g. `weekday09:00 weekend10:00`, `every08:00`) |

## Mute

See [Muting Alerts](mute.md).

| Command | Description |
|---------|-------------|
| `!mute <scope> <value> [duration:<d>]` | Mute a gym / pokemon / area / pokestop / station |
| `!mute everything [duration:<d>]` | Mute ALL alerts |
| `!mute <pokemon> [duration:<d>]` | Shorthand — pokemon scope assumed |
| `!unmute <scope> <value>` | End a mute early |
| `!unmute everything` (or `all`) | Clear all active mutes |

Default duration is 1h if omitted. Per-type aliases (`!raid mute gym ...`, `!gym mute gym ...`) forward to `!mute` with the scope pre-filled.

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
| `!info moves` | List moves + IDs |
| `!info items` | List items the bot recognises (canonical `!quest` tokens) |
| `!info weather` | Current in-game weather + boosted types |
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
| `clean` | Auto-delete expired alerts |
| `edit` | Single message edited in place |
| `remove` | Remove (instead of add) tracking |
| `everything` | All items of that type |

`d:` and `area:` are mutually exclusive. `location:` requires `d:`. See [Areas & Locations](areas.md#per-rule-overrides) for the full rules.

## Testing

| Command | Description |
|---------|-------------|
| `!poracle-test pokemon` | Send a test pokemon alert |
| `!poracle-test raid` | Send a test raid alert |
| `!poracle-test pokestop` | Send a test pokestop alert |
| `!poracle-test gym` | Send a test gym alert |
| `!poracle-test nest` | Send a test nest alert |
| `!poracle-test quest` | Send a test quest alert |
| `!poracle-test fort-update` | Send a test fort update alert |
| `!poracle-test max-battle` | Send a test MAX Battle alert |

## Discord-Specific

| Command | Description |
|---------|-------------|
| `!role list` | List available roles |
| `!role add <name>` | Add a role |
| `!role remove <name>` | Remove a role |
| `!role membership` | Show your roles |
