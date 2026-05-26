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
| Where you can use it | Channel or DM, both visible | DM-scoped — replies are **ephemeral** (only you see them) |
| Discoverability | Memorise the syntax / use `!help` | Discord's UI shows options + descriptions |
| Autocomplete | None | Pokemon names, areas, templates, profiles all autocomplete |
| Admin overrides | `user:` / `name:` for managing other users | Not supported — slash always targets yourself |
| Filter syntax | `iv:90-100 d:500 great:1-10` | One option per filter (e.g. `min_iv: 90`, `max_iv: 100`, `distance: 500`) |

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

- **`/area`** — `add`, `list`, `remove`
- **`/location`** — `add`, `list`, `show`, `remove`, `set-default`, `remove-default`
- **`/profile`** — `list`, `name`, `copy`, `delete`, `settime`, switch-to (numeric)
- **`/summary`** — `quest` (with `settime` / `cleartime` / `now` sub-sub-commands)
- **`/untrack`** — pick the tracking type (pokemon, raid, …) and the target

For example, to save a named location, you'd run `/location add` and fill in the `name` and `coords-or-place` fields in the option panel.

## Autocomplete

Several options have live autocomplete suggestions:

| Option | Suggestions |
|---|---|
| **Pokemon name** (on `/track`, `/quest`, `/raid`, `/nest`, `/maxbattle`) | Matches against names in your language + English + dex ID |
| **Area** (on `/area add`, `/area remove`, `/track area:…`) | Areas your account is allowed to use |
| **Template** (on `/track template:…` and others) | Templates currently loaded on this server (same as `!info templates`) |
| **Profile name** (on `/profile name`, `/profile copy`) | Your existing profile names |
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
- `min_iv: 90`
- `max_iv: 100`
- `distance: 500`

Press Enter. Poracle replies ephemerally — only you see the confirmation.

### Save a named location

Text:

```
!location add Home 10 Downing Street, London
```

Slash: `/location add` then fill in:

- `name: Home`
- `coords-or-place: 10 Downing Street, London`

### Set a quest summary schedule

Text:

```
!summary quest settime weekday07:30 weekend10:00
```

Slash: `/summary quest settime` then:

- `times: weekday07:30 weekend10:00`

## Limitations

- **No `user:` / `name:` overrides.** Slash always targets you personally — there's no admin-on-behalf-of-someone-else surface. Admins who need that keep using the text form.
- **No public/channel responses.** All replies are ephemeral DMs. Poracle conversations are private by nature.
- **Discord caps options at 25 per command.** A few less-common text-command filters (move, weight, individual-stat ranges) aren't surfaced as their own slash option — for those, keep using the text form.
- **Operator can restrict the surface.** Admins can choose a subset via `enable = [...]` in `[discord.slash_commands]`. If a particular command isn't showing up, that's why.

## Text-Only Commands

These remain text-only on purpose — they're either admin operations or sensitive enough that the text form is the right surface:

`!poracle`, `!poracle-test`, `!broadcast`, `!apply`, `!enable`, `!disable`, `!unregister`, `!channel`, `!webhook`, `!autocreate`, `!role`, `!ask`, `!script`, `!userlist`, `!backup`, `!restore`, `!weather`.

## Related

- [Command Reference](commands.md) — the full text-command surface (slash uses the same filters under the hood).
- [Registration](registration.md) — slash commands work the same way after registration as text does.
