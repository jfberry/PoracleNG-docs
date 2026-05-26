# Quest Tracking

Track field research quests by their rewards — pokemon encounters, items, stardust, mega energy, and candy.

!!! tip "Filter syntax"
    Filters use `key:value` form — `d:500`, `area:downtown`, `template:2`, `stardust:1000`, `energy:charizard`. Legacy `d500` / `stardust500` are still accepted. Multi-word item names use **underscores**: `rare_candy`, `golden_razz_berry`. See [Pokemon Tracking — Filter Syntax](pokemon.md#filter-syntax).

## Pokemon Encounter Rewards

=== "Discord"

    ```
    !quest spinda                       # Quests rewarding Spinda
    !quest pikachu                      # Quests rewarding Pikachu
    !quest everything                   # All quest rewards
    !quest pikachu charmander d:500     # Either pokemon as reward, within 500m
    ```

=== "Telegram"

    ```
    /quest spinda
    /quest everything
    ```

### Shiny-Eligible Quests

=== "Discord"

    ```
    !quest shiny spinda                 # Spinda quests (shiny possible)
    ```

=== "Telegram"

    ```
    /quest shiny spinda
    ```

## Stardust Rewards

=== "Discord"

    ```
    !quest stardust                     # Any stardust reward
    !quest stardust:500                 # Stardust reward >= 500
    !quest stardust:1000                # Stardust reward >= 1000
    ```

=== "Telegram"

    ```
    /quest stardust
    /quest stardust:500
    ```

## Mega Energy Rewards

=== "Discord"

    ```
    !quest energy                       # All mega energy rewards
    !quest energy:charizard             # Charizard mega energy specifically
    !quest energy:blastoise             # Blastoise mega energy
    ```

=== "Telegram"

    ```
    /quest energy
    /quest energy:charizard
    ```

## Candy Rewards

=== "Discord"

    ```
    !quest candy                        # All candy rewards
    !quest candy:pikachu                # Pikachu candy specifically
    !quest candy:lapras                 # Lapras candy
    ```

=== "Telegram"

    ```
    /quest candy
    /quest candy:pikachu
    ```

## Item Rewards

Track specific item rewards. **Multi-word names use underscores**:

=== "Discord"

    ```
    !quest rare_candy                   # Rare candy rewards
    !quest silver_pinap_berry           # Silver Pinap Berry
    !quest golden_razz_berry            # Golden Razz Berry
    !quest fast_tm charged_tm           # Multiple items, OR-matched
    ```

=== "Telegram"

    ```
    /quest rare_candy
    /quest silver_pinap_berry
    ```

Use `!info items` to see every item name the bot recognises — the list it prints is the exact token to use (with spaces replaced by underscores).

## Distance and Areas

=== "Discord"

    ```
    !quest pikachu d:500                # Pikachu reward within 500m
    !quest rare_candy area:city_centre  # Rare candy quests in one area
    !quest pikachu d:500 location:Work  # Within 500m of "Work" named location
    ```

=== "Telegram"

    ```
    /quest pikachu d:500
    /quest rare_candy area:city_centre
    ```

See [Areas & Locations](areas.md#per-rule-overrides) for the full `area:` / `location:` rules.

## All Quest Options

| Option | Description | Example |
|--------|-------------|---------|
| `<pokemon>` | Quest rewarding a pokemon encounter | `spinda` |
| `stardust` / `stardust:<n>` | Stardust reward (any, or minimum amount) | `stardust:500` |
| `energy` / `energy:<pokemon>` | Mega energy (all, or specific) | `energy:charizard` |
| `candy` / `candy:<pokemon>` | Candy (all, or specific) | `candy:lapras` |
| `<item_name>` | Specific item reward (underscores for multi-word) | `rare_candy` |
| `shiny` | Shiny-eligible encounters | `shiny` |
| `d:<n>` | Distance in metres | `d:500` |
| `location:<name>` | Distance from a named location (requires `d:`) | `location:Home` |
| `area:<name>[,<name>]` | Restrict to specific areas | `area:downtown` |
| `template:<n>` | Alert template | `template:2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `edit` | Single message, edited in place | `edit` |
| `summary` | Buffer matches for grouped delivery on a schedule | `summary` |
| `everything` | All quest rewards | `everything` |

Legacy no-colon forms (`stardust500`, `energycharizard`, `candypikachu`, `d500`) still work.

## Buffered Summary Delivery

Instead of receiving a separate message for every matched quest, you can opt a rule into **summary delivery**: matched quests are held in memory and dispatched as grouped messages on a schedule you set (e.g. one digest at 07:30 each weekday morning, grouped by reward).

Add `summary` to any `!quest` rule:

=== "Discord"

    ```
    !quest pikachu summary              # Pikachu quests, summary delivery
    !quest rare_candy summary           # Rare Candy quests, summary delivery
    !quest stardust:1000 summary        # Stardust >= 1000, summary delivery
    ```

=== "Telegram"

    ```
    /quest pikachu summary
    /quest rare_candy summary
    ```

`!tracked` shows `summary` next to each rule that uses it.

A grouped message lists every pokestop with the same reward in one embed, with a multi-pin static map showing all of them. Large groups split into multiple messages automatically so they stay under Discord's embed limits.

Schedule when those grouped messages fire — and flush the buffer on demand — with the [`!summary` command](summaries.md). Without a schedule, matched quests accumulate but are never dispatched until you either run `!summary quest now` or set a delivery time.

!!! note
    Summary delivery is currently supported only for **quest** tracking. Other alert types (raids, invasions, etc.) may gain summary support in future releases.

## Removing Tracks

=== "Discord"

    ```
    !quest remove spinda                # Stop tracking Spinda quests
    !quest remove stardust              # Stop tracking stardust quests
    !quest remove all_items             # Every item-reward rule
    !quest remove all_pokemon           # Every pokemon-reward rule
    !quest remove everything            # Remove all quest tracking
    !untrack id:<N>                     # Remove by UID (see !tracked)
    ```

=== "Telegram"

    ```
    /quest remove spinda
    /quest remove everything
    /untrack id:12
    ```
