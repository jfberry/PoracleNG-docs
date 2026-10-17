# Discord Slash Commands

PoracleNG can expose its user-facing commands as Discord **slash commands** (`/track`, `/quest`, `/area`, …) in parallel with the existing text-prefix surface (`!track`, `!quest`, `!area`, …). Slash is **optional** — the operator turns it on per server.

If your server has slash commands enabled, type `/` in any channel where the bot is present and Discord's UI will suggest the commands and their options.

## Is It Enabled?

Type `/` in the Poracle DM (or any channel where the bot is allowed). If you see Poracle commands in Discord's autocomplete list, it's enabled. If you don't, ask your admin to add this to `config.toml`:

```toml
[discord.slash_commands]
enabled = true
```

Existing `!` text commands keep working either way — slash is a parallel UI, not a replacement.

## How Slash Differs From Text

| | Text (`!track`) | Slash (`/track`) |
|---|---|---|
| Where you can use it | Channel or DM, both visible | Channel or DM — replies are **ephemeral** (only you see them, even in a channel) |
| Discoverability | Memorise the syntax / use `!help` | Discord's UI shows options + descriptions |
| Autocomplete | None | Pokemon names, forms, costumes, areas, templates, profiles, help topics all autocomplete |
| Admin overrides | `user:` / `name:` for managing other users | Not supported — slash always targets yourself |
| Filter syntax | `iv:90-100 d:500 great:1-10` | One option per filter (e.g. `iv: 90-100`, `distance: 500`, `great_rank: 10`) |

Slash and text commands hit the **same backend**. A `/track` rule shows up in `!tracked`, can be removed with `!untrack`, and uses the same templates and areas as anything you set up with `!track`.

## Available Commands

The full list of slash commands surfaced on this branch:

| Group | Commands |
|---|---|
| Account / status | `/version`, `/tracked`, `/help`, `/info`, `/language`, `/start`, `/stop` |
| Tracking | `/track`, `/raid`, `/egg`, `/quest`, `/invasion`, `/incident`, `/lure`, `/nest`, `/maxbattle`, `/gym`, `/fort`, `/untrack` |
| Areas / locations / profiles | `/area`, `/location`, `/profile` |
| Schedules | `/summary` |

Type `/` and start typing the command name; Discord will show you the available options as you go.

### Sub-commands

A few commands group their sub-actions under one slash command:

- **`/area`** — `add`, `remove`, `show`, `list`, `overview`
- **`/location`** — `add` (`name`, `place`), `list`, `show`, `remove`, `set-default` (`place`), `remove-default`
- **`/profile`** — `list`, `change`, `create`, `delete`, `settime` (`times`), `cleartime`, `copyto`
- **`/info`** — `pokemon`, `rarity`, `shiny`, `weather` (other `!info` lookups such as `templates`, `moves`, `items`, `costumes` are text-only)
- **`/summary quest`** — `show`, `settime`, `cleartime`, `now`
- **`/untrack`** — one sub-command per type: `pokemon`, `raid`, `egg`, `quest`, `invasion`, `lure`, `nest`, `gym`, `fort`, `maxbattle`. Pick a rule from the `tracking` dropdown, or choose **Remove ALL** at the top of the list. (Incidents are removed with `!incident remove …` in text.)

For example, to save a named location, you'd run `/location add` and fill in the `name` and `place` fields in the option panel.

### Tracking Options

| Command | Options |
|---|---|
| `/track` | `pokemon` (required, or `everything`), `iv` (e.g. `100`, `90-100`), `distance`, `great_rank`, `ultra_rank`, `little_rank`, `clean`, `edit` (5.3+), `template`, `form`, `costume`, `size`, `location`, `areas` |
| `/raid` | `boss`, `level`, `team`, `distance`, `clean`, `edit`, `template`, `form`, `costume`, `location`, `areas` |
| `/egg` | `level` (required), `team`, `distance`, `clean`, `edit`, `template`, `location`, `areas` |
| `/quest` | `pokemon`, `item`, `stardust`, `pokecoins`, `candy`, `mega_energy`, `min_amount`, `distance`, `clean`, `summary`, `template`, `location`, `areas` |
| `/invasion` | `grunt_type` (required), `gender`, `distance`, `clean`, `template`, `location`, `areas` |
| `/incident` | `type` (required), `distance`, `clean`, `template`, `location`, `areas` |
| `/lure` | `lure_type` (required), `distance`, `clean`, `edit`, `template`, `location`, `areas` |
| `/nest` | `pokemon` (required), `min_spawn_avg`, `distance`, `clean`, `template`, `location`, `areas` |
| `/maxbattle` | `level`, `pokemon`, `gmax`, `distance`, `clean`, `template`, `location`, `areas` |
| `/gym` | `team`, `slot_changes`, `battle_changes`, `distance`, `clean`, `template`, `location`, `areas` |
| `/fort` | `fort_type` (required: Pokestop, Gym, Station or Everything), `include_empty`, `distance`, `template`, `location`, `areas` |

!!! note "5.3+"
    Before 5.3, `/fort` offered only Pokestop and Gym, and it also showed a `clean` option that made the command fail when set (fort rules have no `clean`). The `clean` option is gone.

Anything not listed (individual-stat ranges, CP, level, `move:`, `gen:`, PVP `mega`, rarity, `ex`, `rsvp`, …) is text-only.

## Autocomplete

Several options have live autocomplete suggestions:

| Option | Suggestions |
|---|---|
| **Pokemon name** (on `/track`, `/quest`, `/raid`, `/nest`, `/maxbattle`) | Matches against names in your language + English + dex ID |
| **Area** (on `/area add`, `/area remove`, and the `areas` option on tracking commands) | Areas your account is allowed to use. Pick several — each pick is added to a comma-separated list |
| **Template** (on `/track template:…` and others) | Templates currently loaded on this server (same as `!info templates`) |
| **Profile** (on `/profile change`, `/profile delete`, `/profile copyto`) | Your existing profiles, shown as `name [#number]`; picking one sends the profile number |
| **Times** (on `/profile settime`, `/summary quest settime`) (5.3+) | Ready-made time specs such as `weekday07:30`, `weekend07:00-18:00` or `08:00-20:00/2`, each with a short description. Type a day prefix (`mon`, `w`…) to narrow them, or a digit to complete a time; whatever you've typed is always offered first so you can keep a custom spec |
| **Form / costume** (on `/track`, `/raid`) | Forms/costumes for the chosen pokemon, with recently-seen ones listed first |
| **Help topic** (on `/help`) | Available help topics (admin topics hidden for non-admins) |
| **Tracking rule** (on `/untrack …`) | Your existing rules of that type, plus "Remove ALL" |
| **Raid boss / quest reward** (on `/raid`, `/quest`) | Things actually seen on the scanner in the last few hours — populated live from webhook traffic |

Start typing in the option box — Discord will narrow the list as you type. The dropdown is capped at 25 suggestions; if what you want isn't shown, type more characters.

## Worked Examples

### Track a high-IV Pikachu nearby

Text:

```
!track pikachu iv:90-100 d:500
```

Slash: `/track` then fill in:

- `pokemon: pikachu`
- `iv: 90-100`
- `distance: 500`

Press Enter. Poracle replies ephemerally — only you see the confirmation.

### Save a named location

Text:

```
!location add Home 10 Downing Street, London
```

Slash: `/location add` then fill in:

- `name: Home`
- `place: 10 Downing Street, London`

### Set a quest summary schedule

Text:

```
!summary quest settime weekday07:30 weekend10:00
```

Slash: `/summary quest settime` then:

- `times: weekday07:30,weekend10:00`

The `times` option takes one or more specs separated by commas (or spaces), using the same [time-spec grammar](profiles.md#time-spec-grammar) as the text command. Its autocomplete (5.3+) suggests one spec at a time, so it's most useful for a single spec; for several, type them out.

## Limitations

- **No `user:` / `name:` overrides.** Slash always targets you personally — there's no admin-on-behalf-of-someone-else surface. Admins who need that keep using the text form.
- **Replies are ephemeral.** Used in a channel or a DM, the response is visible only to you. Slash invocations are recorded in the operator's DM log channel if one is configured.
- **Same restrictions as text.** `disabled_commands` and `command_security` apply to slash commands exactly as they do to `!` commands, and you must be registered (except for `/version`).
- **Discord caps options at 25 per command.** Less-common text filters (moves, individual-stat ranges, CP/level, PVP mega, …) aren't surfaced as slash options — use the text form for those.
- **Operator can restrict the surface.** Admins can choose a subset via `enable = [...]` in `[discord.slash_commands]`. If a particular command isn't showing up, that's why.

## Text-Only Commands

These remain text-only — they're either admin operations or don't fit the slash model:

`!poracle`, `!poracle-test`, `!poracle-admin`, `!poracle-clean`, `!poracle-id`, `!poracle-emoji`, `!broadcast`, `!apply`, `!enable`, `!disable`, `!unregister`, `!channel`, `!webhook`, `!autocreate`, `!community`, `!role`, `!ask`, `!script`, `!userlist`, `!backup`, `!restore`, `!mute`, `!unmute`.

## Related

- [Command Reference](commands.md) — the full text-command surface (slash uses the same filters under the hood).
- [Registration](registration.md) — slash commands work the same way after registration as text does.
