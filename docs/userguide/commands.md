# Command Reference

Quick reference for all PoracleNG user commands. Discord uses `!` prefix, Telegram uses `/`.

## Registration & Status

| Command | Description |
|---------|-------------|
| `!poracle` | Register with the bot |
| `!unregister` | Remove your registration and all data |
| `!start` | Resume alerts (after stop) |
| `!stop` | Pause all alerts |
| `!tracked` | Show all your tracking subscriptions |
| `!version` | Show bot version |
| `!help` | Show help |
| `!help <command>` | Show help for specific command |

## Location & Areas

| Command | Description |
|---------|-------------|
| `!location <lat>,<lon>` | Set your location |
| `!location <place>` | Set location by place name |
| `!location remove` | Remove your location |
| `!area list` | List available areas |
| `!area add <name>` | Add one or more areas |
| `!area remove <name>` | Remove an area |
| `!area remove everything` | Remove all areas |

## Pokemon Tracking

| Command | Description |
|---------|-------------|
| `!track <pokemon> [options]` | Track a pokemon |
| `!track everything [options]` | Track all pokemon (with filters) |
| `!untrack <pokemon>` | Stop tracking a pokemon |
| `!untrack everything` | Remove all pokemon tracking |

### Pokemon Options

| Option | Description | Example |
|--------|-------------|---------|
| `iv<n>` | Min IV % | `iv90`, `iv90-100` |
| `maxiv<n>` | Max IV % | `maxiv50` |
| `level<n>` | Min level | `level35`, `level30-35` |
| `maxlevel<n>` | Max level | `maxlevel30` |
| `cp<n>` | Min CP | `cp2000`, `cp2000-3000` |
| `maxcp<n>` | Max CP | `maxcp1500` |
| `atk<n>` | Min attack | `atk15`, `atk14-15` |
| `def<n>` | Min defense | `def15` |
| `sta<n>` | Min stamina | `sta15` |
| `maxatk<n>`, `maxdef<n>`, `maxsta<n>` | Max stat | `maxatk10` |
| `d<n>` | Distance (meters) | `d500` |
| `t<n>` | Min time remaining (sec) | `t300` |
| `form:<name>` | Form name | `form:alolan` |
| `gen<n>` | Generation | `gen1` |
| `rarity<n>` | Min rarity (1-5) | `rarity3` |
| `maxrarity<n>` | Max rarity | `maxrarity4` |
| `size<n>` | Min size (1-5) | `size5` |
| `maxsize<n>` | Max size | `maxsize3` |
| `weight<n>` | Min weight | `weight100` |
| `maxweight<n>` | Max weight | `maxweight50` |
| `great<n>` | Great League rank | `great5`, `great1-5` |
| `ultra<n>` | Ultra League rank | `ultra10` |
| `little<n>` | Little League rank | `little5` |
| `greatcp<n>`, `ultracp<n>`, `littlecp<n>` | League min CP | `greatcp1400` |
| `cap<n>` | PVP level cap | `cap50` |
| `male`, `female`, `genderless` | Gender filter | `male` |
| `template<n>` | Template number | `template2` |
| `clean` | Auto-delete expired | `clean` |
| `everything` | All pokemon | `everything` |
| `individually` | Track as individual rows | `individually` |

## Raid & Egg Tracking

| Command | Description |
|---------|-------------|
| `!raid <pokemon/level> [options]` | Track raids |
| `!raid remove <target>` | Remove raid tracking |
| `!egg <level> [options]` | Track eggs |
| `!egg remove <level>` | Remove egg tracking |

### Raid/Egg Options

`level<n>`, `ex`, `d<n>`, team names (`instinct`/`valor`/`mystic`/`harmony`), `move:<name>`, `template<n>`, `clean`, `everything`, `remove`

## MAX Battle Tracking

| Command | Description |
|---------|-------------|
| `!maxbattle level<n> [options]` | Track MAX Battles by level (1-5) |
| `!maxbattle <pokemon> [options]` | Track MAX Battles for a specific pokemon |
| `!maxbattle everything [options]` | Track all MAX Battle levels |
| `!maxbattle remove <target>` | Remove MAX Battle tracking |
| `!maxbattle remove everything` | Remove all MAX Battle tracking |

### MAX Battle Options

| Option | Description | Example |
|--------|-------------|---------|
| `level<n>` | MAX Battle level | `level5` |
| `gmax` | Gigantamax variant only | `gmax` |
| `d<n>` | Distance (meters) | `d500` |
| `move/<name>` | Filter by move | `move/flamethrower` |
| `gen<n>` | Generation filter | `gen1` |
| `form:<name>` | Form filter | `form:alolan` |
| `template<n>` | Template number | `template2` |
| `clean` | Auto-delete expired | `clean` |

For more details see [MAX Battle Tracking](maxbattle.md).

## Other Tracking

| Command | Description |
|---------|-------------|
| `!quest <reward> [options]` | Track quest rewards |
| `!quest remove <reward>` | Remove quest tracking |
| `!incident <type> [options]` | Track invasions |
| `!incident remove <type>` | Remove invasion tracking |
| `!lure <type> [options]` | Track lures |
| `!lure remove <type>` | Remove lure tracking |
| `!nest <pokemon> [options]` | Track nests |
| `!nest remove <pokemon>` | Remove nest tracking |
| `!gym <team> [options]` | Track gym changes |
| `!gym remove <team>` | Remove gym tracking |
| `!fort [options]` | Track fort updates (new/removed/renamed stops and gyms) |
| `!weather` | Track weather changes |

## Profiles

| Command | Description |
|---------|-------------|
| `!profile list` | List your profiles |
| `!profile <n>` | Switch to profile N |
| `!profile name <name>` | Name current profile |
| `!profile copy <n>` | Copy current to profile N |
| `!profile delete <n>` | Delete profile N |

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
| `d<n>` | Distance from location in meters |
| `template<n>` | Alert template number |
| `clean` | Auto-delete expired alerts |
| `remove` | Remove (instead of add) tracking |
| `everything` | All items of that type |

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
