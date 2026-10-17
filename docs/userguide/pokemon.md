# Pokemon Tracking

Track wild pokemon spawns with customizable filters for IVs, level, CP, PVP rank, rarity, and more.

## Filter Syntax

All filters use `key:value` form — e.g. `iv:90`, `cp:2000-3000`, `d:500`. **Ranges** are written `low-high` (e.g. `iv:90-100`, `level:25-35`) and are the preferred way to set both a minimum and a maximum.

Bare minimum-with-colon (`iv:99` to mean "at least 99% IV") is fine and common — the range form is just what you reach for when you also want an upper bound. Always prefer `iv:90-100` over `miniv:90 maxiv:100`; the range reads better and is easier to edit.

!!! note "Legacy forms still work"
    Older syntax (`iv90`, `cp2000`, `d500`, `maxiv50`) is still accepted, but documentation and examples use the colon/range form throughout. If you're scripting, prefer the colon form going forward.

## Basic Usage

=== "Discord"

    ```
    !track <pokemon_name> [options...]
    ```

=== "Telegram"

    ```
    /track <pokemon_name> [options...]
    ```

## Examples

### Track a Specific Pokemon

=== "Discord"

    ```
    !track pikachu
    !track 25
    ```

=== "Telegram"

    ```
    /track pikachu
    /track 25
    ```

You can use either the pokemon name or its Pokedex number.

### Track with IV Filter

=== "Discord"

    ```
    !track pikachu iv:90          # IV 90% or higher
    !track pikachu iv:90-100      # IV between 90% and 100%
    !track gible iv:0             # IV 0% (nundo)
    ```

=== "Telegram"

    ```
    /track pikachu iv:90
    /track pikachu iv:90-100
    /track gible iv:0
    ```

### Track by Level and CP

=== "Discord"

    ```
    !track pikachu level:35              # Level 35+
    !track pikachu level:30-35           # Level 30 to 35
    !track pikachu cp:2000               # CP 2000+
    !track pikachu cp:2000-3000          # CP between 2000-3000
    ```

=== "Telegram"

    ```
    /track pikachu level:35
    /track pikachu level:30-35
    /track pikachu cp:2000
    ```

### Track by Individual Stats

=== "Discord"

    ```
    !track pikachu atk:15                       # Attack 15
    !track pikachu atk:15 def:15 sta:15         # Perfect IVs (specific stats)
    !track pikachu atk:0 def:0 sta:0            # Nundo (all stats 0)
    !track pikachu atk:13-15 def:13-15 sta:13-15 # Near-perfect ranges
    ```

=== "Telegram"

    ```
    /track pikachu atk:15
    /track pikachu atk:15 def:15 sta:15
    ```

### Track by Distance

=== "Discord"

    ```
    !track pikachu iv:100 d:500          # Within 500m of your default location
    !track pikachu iv:90 d:2000          # Within 2km
    !track pikachu iv:100 d:500 location:Work  # 500m of your named "Work" location
    ```

=== "Telegram"

    ```
    /track pikachu iv:100 d:500
    /track pikachu iv:90 d:2000
    ```

!!! note
    Distance tracking requires a location to be set with `!location`. See [Areas & Locations](areas.md) for default + named locations and per-rule overrides.

### Track in Specific Areas

=== "Discord"

    ```
    !track pikachu iv:100 area:downtown          # Restricted to one area
    !track pikachu iv:100 area:downtown,uptown   # Or several
    ```

=== "Telegram"

    ```
    /track pikachu iv:100 area:downtown,uptown
    ```

`area:` and `d:` are mutually exclusive — see [Per-rule overrides](areas.md#per-rule-overrides).

### Track Everything

=== "Discord"

    ```
    !track everything iv:100             # All pokemon with 100% IV
    !track everything iv:0               # All nundos
    !track everything level:35           # All level 35+
    ```

=== "Telegram"

    ```
    /track everything iv:100
    /track everything iv:0
    /track everything level:35
    ```

!!! warning
    `!track everything` without filters is not allowed for non-admin users to prevent alert floods.

    The check looks for a filter that genuinely narrows the set: IV, CP, level, ATK/DEF/STA, PVP league, type, generation, gender, distance, time or `costume:` all count. These do **not** count on their own:

    - `shiny` — almost every species can be shiny (see the note on `shiny` below)
    - `form:`, `template:`, `clean` / `edit`
    - `size:m` / `rarity:common` (or a range that still includes them) — these classes cover most spawns. A size or rarity filter that *excludes* the common class (e.g. `size:xxl`, `rarity:3-5`) does count.

### Track by Type

Type names narrow `everything` or a list of named pokemon — they are **not** a pokemon selector on their own. `!track dragon iv:90` (no pokemon, no `everything`) matches nothing and replies with an error.

=== "Discord"

    ```
    !track everything dragon iv:90             # All dragon-type pokemon with 90%+ IV
    !track everything water fire iv:100        # All water and fire types with 100% IV
    !track dratini+ charmander+ dragon iv:90   # Only the dragon types from these lines
    ```

=== "Telegram"

    ```
    /track everything dragon iv:90
    /track everything water fire iv:100
    ```

Using a type (or `gen:`) with `everything` stores one rule per matching species rather than a single catch-all rule.

### Track by Generation

Like types, `gen:` narrows `everything` (or a list of named pokemon); it is not a selector on its own.

=== "Discord"

    ```
    !track everything gen:1 iv:100       # All gen 1 pokemon with 100% IV
    !track everything gen:4 iv:90        # All gen 4 with 90%+
    ```

=== "Telegram"

    ```
    /track everything gen:1 iv:100
    ```

### Track by Form

=== "Discord"

    ```
    !track pikachu form:libre            # Specific form
    !track castform form:sunny
    !track vulpix form:alolan
    ```

=== "Telegram"

    ```
    /track pikachu form:libre
    ```

`form:` works with `everything` too — `!track everything form:alola iv:90` creates one rule for every species that has an Alola form. Multi-word form names can be typed with spaces or underscores. Use `!info <pokemon>` (or `!info <pokemon> forms` for the full list) to see form names — recently-seen forms are listed first. An unknown form name is rejected rather than silently ignored.

### Track by Costume

=== "Discord"

    ```
    !track pikachu costume:holiday_2016  # Only that costume
    !track pikachu costume:5             # Costume by ID
    !track pikachu costume:0             # Only pokemon with NO costume
    !track everything costume:5 iv:90    # Any species wearing costume 5
    ```

=== "Telegram"

    ```
    /track pikachu costume:holiday_2016
    /track pikachu costume:0
    ```

Without `costume:` a rule matches any costume (or none). `costume:0` means "no costume". Use `!info costumes` for the full id/name list, or `!info <pokemon> costumes` for costumes recently seen on that species. An unknown costume name is rejected.

### Track by Rarity

=== "Discord"

    ```
    !track everything rarity:4           # Very rare pokemon
    !track everything rarity:5           # Ultra rare pokemon
    !track everything rarity:3-5         # Rare to ultra rare
    ```

=== "Telegram"

    ```
    /track everything rarity:4
    ```

Rarity levels: 1 `common`, 2 `uncommon`, 3 `rare`, 4 `very-rare`, 5 `ultra-rare`, 6 `unseen`. Rarity is calculated from what your server's scanner has seen recently; use `!info rarity` to see which species are in each tier.

!!! note "Single value = exact match"
    A single rarity or size value is an **exact** match: `rarity:4` means Very Rare only, *not* "Very Rare or rarer". Use a range (`rarity:4-5`) or `maxrarity:` to cover several tiers.

Names work as well as numbers — `rarity:rare`, `rarity:uncommon-rare`, `rarity:rare-ultra-rare`, `maxrarity:very-rare`. Because `-` separates a range, the hyphenated names `very-rare` / `ultra-rare` only work in `maxrarity:` or as the *second* half of a range: `rarity:very-rare` on its own is not recognised, so use `rarity:4` / `rarity:5` instead.

!!! note
    Tier 6 (`unseen`) is accepted, but the server never currently assigns it — species with no recent sightings have no rarity tier — so a `rarity:6` rule won't match anything.

### Track by Size

=== "Discord"

    ```
    !track pikachu size:xxs              # XXS only
    !track pikachu size:xxl              # XXL only
    !track pikachu size:xl-xxl           # XL or XXL
    !track everything size:5 iv:90       # XXL anything with 90%+ IV
    ```

=== "Telegram"

    ```
    /track pikachu size:xxs
    /track pikachu size:xl-xxl
    ```

Sizes: 1 `xxs`, 2 `xs`, 3 `m`, 4 `xl`, 5 `xxl`. As with rarity, a single value is an exact match.

!!! note "Weight filters removed"
    `weight:` / `maxweight:` are no longer accepted — the scanner's weight value is unreliable (it is reset whenever the pokemon's display changes). Existing rules that already had a weight filter keep it.

### Track Evolutions

Append `+` to a pokemon name to include its entire evolution line:

=== "Discord"

    ```
    !track eevee+ iv:100                 # Eevee and all eeveelutions
    !track bulbasaur+ iv:90              # Bulbasaur, Ivysaur, Venusaur
    ```

=== "Telegram"

    ```
    /track eevee+ iv:100
    ```

### Track with Gender Filter

=== "Discord"

    ```
    !track pikachu female iv:100         # Female only
    !track pikachu male iv:90            # Male only
    ```

=== "Telegram"

    ```
    /track pikachu female iv:100
    ```

### Track PVP Ranks

See [PVP Tracking](pvp.md) for the full PVP surface.

=== "Discord"

    ```
    !track pikachu great:1-5             # Great League rank 1-5
    !track pikachu ultra:1-10            # Ultra League rank 1-10
    !track pikachu little:1-5            # Little League rank 1-5
    ```

=== "Telegram"

    ```
    /track pikachu great:1-5
    ```

### Ping someone with the alert

On Discord, @mentioning a user or role in a `!track` command stores the mention with the rule. When the rule fires, the mention is added to the alert so it actually pings — useful in shared channels.

```
!track dratini iv:100 @Rare-Hunters
```

### Shiny

`shiny` is accepted by `!track` for compatibility but currently has no effect on matching — the rule is stored exactly as if `shiny` had not been typed.

### Clean and Edit

`clean` deletes the alert when the pokemon despawns. `edit` controls what happens when the scanner sends an update for a pokemon you have already been alerted about (for example IVs arriving after an initial no-IV sighting, or a weather-boost change):

| Rule has `edit` | What you get on an update |
|-----------------|---------------------------|
| No | A new alert posted as a **threaded reply** to the original |
| Yes | The original alert is **edited in place** |

If the pokemon changes so that it **no longer matches** your rule (for example a Ditto reveal, a form/gender change, or a weather change that drops the boost), you receive a `monsterChanged` follow-up — again as a threaded reply, or an in-place edit with `edit` — so you know the original alert is no longer accurate. Your admin can turn change tracking off (`pokemon_change_tracking`), in which case updates arrive as ordinary new alerts.

=== "Discord"

    ```
    !track dratini iv:90 edit
    !track dratini iv:90 clean edit
    ```

=== "Telegram"

    ```
    /track dratini iv:90 edit
    ```

!!! note "5.3+"
    On 5.2.x `edit` is accepted on `!track` but pokemon alerts are not edited. From 5.3, pokemon edits happen only while the original alert is recent (default 5 minutes, `pokemon_edit_window_mins`) — older alerts get a threaded reply instead so the update isn't lost up a busy channel. Admins can also make editing the default for an alert type (`[tracking] default_edit`); `!tracked` then shows those rules as editing even without the `edit` keyword.

## All Options

| Option | Description | Example |
|--------|-------------|---------|
| `iv:<n>` | Minimum IV percentage | `iv:90` |
| `iv:<low>-<high>` | IV range (preferred over min/max) | `iv:90-100` |
| `cp:<n>` | Minimum CP | `cp:2000` |
| `cp:<low>-<high>` | CP range | `cp:2000-3000` |
| `level:<n>` | Minimum pokemon level | `level:35` |
| `level:<low>-<high>` | Pokemon level range | `level:30-35` |
| `miniv:<n>`, `maxiv:<n>` | Single-sided IV bound | `maxiv:0` |
| `mincp:<n>`, `maxcp:<n>` | Single-sided CP bound | `maxcp:1500` |
| `maxlevel:<n>` | Maximum level | `maxlevel:30` |
| `atk:<n>`, `def:<n>`, `sta:<n>` | Minimum individual IV (0-15) | `atk:15` |
| `atk:<low>-<high>` etc. | Individual IV range | `atk:13-15` |
| `maxatk:<n>`, `maxdef:<n>`, `maxsta:<n>` | Maximum individual IV | `maxatk:0` |
| `d:<n>` | Distance in metres from your location | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `t:<n>` | Minimum time remaining (seconds) | `t:300` |
| `form:<name>` | Specific form name | `form:alolan` |
| `costume:<name\|id>` | Specific costume (`0` = no costume) | `costume:0` |
| `<type>` | Narrow `everything`/named pokemon by type | `dragon` |
| `gen:<n>` | Narrow `everything`/named pokemon by generation | `gen:1` |
| `rarity:<n>` or `rarity:<low>-<high>` | Rarity tier (1-6); single value = exact | `rarity:3-5` |
| `maxrarity:<n\|name>` | Maximum rarity tier | `maxrarity:ultra-rare` |
| `size:<n>` or `size:<low>-<high>` | Size (1-5 or `xxs`..`xxl`); single value = exact | `size:xxl` |
| `maxsize:<n\|name>` | Maximum size | `maxsize:xs` |
| `great:<n>`, `ultra:<n>`, `little:<n>` | PVP league rank (single or range) | `great:1-10` |
| `greathigh:<n>`, `ultrahigh:<n>`, `littlehigh:<n>` | Best (lowest) PVP rank to accept | `greathigh:3` |
| `greatcp:<n>`, `ultracp:<n>`, `littlecp:<n>` | Minimum league CP | `greatcp:1400` |
| `cap:<n>` | PVP level cap | `cap:50` |
| `mega`, `mega:x`, `mega:y` | PVP rank as a mega evolution (with a league filter) | `mega:x` |
| `male` / `female` / `genderless` | Gender filter | `male` |
| `template:<n>` | Alert template (see `!info templates`) | `template:2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `edit` | Edit the original alert on updates instead of replying (5.3+) | `edit` |
| `@mention` | Discord: ping this user/role with the alert | `@Hunters` |
| `everything` | All pokemon | `everything` |
| `individually` | Track as individual rows | `individually` |

Legacy no-colon forms (`iv90`, `cp2000`, `d500`, `maxiv50`) are still accepted, but the colon and range forms are recommended.

PVP ranks are limited by your admin's configuration: the worst rank you can ask for is capped at `[pvp] filter_max_rank`, and the minimum league CP is raised to at least `filter_great_min_cp` / `filter_ultra_min_cp` / `filter_little_min_cp`. See [PVP Tracking](pvp.md).

## Removing Tracks

=== "Discord"

    ```
    !untrack pikachu                     # Remove every pikachu rule (all forms/leagues)
    !untrack pikachu costume:5           # Only pikachu rules for costume 5
    !untrack gen:1                       # Every rule for a gen 1 species
    !untrack dragon                      # Every rule for a dragon-type species
    !untrack everything                  # Remove all pokemon tracking
    !untrack id:45                       # Remove one specific rule by UID (see !tracked)
    !track remove pikachu                # Same as !untrack pikachu
    ```

=== "Telegram"

    ```
    /untrack pikachu
    /untrack everything
    /untrack id:45
    ```

`form:` is not accepted when removing — `!untrack <pokemon>` removes all forms; use `id:` to remove a single rule. `!untrack gen:N` and `!untrack <type>` remove rules for individual species only; they don't touch an `everything` catch-all rule.

### Removing other alert types

If the first word after `!untrack` is another alert type, the command is passed to that type's command as a removal:

```
!untrack raid id:12                  # Same as !raid remove id:12
!untrack quest everything            # Same as !quest remove everything
```

Recognised types: `raid`, `egg`, `quest`, `invasion`, `incident`, `lure`, `nest`, `gym`, `fort`, `maxbattle`.

## Viewing Your Tracks

=== "Discord"

    ```
    !tracked
    ```

=== "Telegram"

    ```
    /tracked
    ```
