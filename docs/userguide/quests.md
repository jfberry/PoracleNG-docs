# Quest Tracking

Track field research quests by their rewards — pokemon encounters, items, stardust, mega energy, and candy.

## Pokemon Encounter Rewards

=== "Discord"

    ```
    !quest spinda                       # Quests rewarding Spinda
    !quest pikachu                      # Quests rewarding Pikachu
    !quest everything                   # All quest rewards
    ```

=== "Telegram"

    ```
    /quest spinda
    /quest everything
    ```

### Shiny-Eligible Quests

=== "Discord"

    ```
    !quest spinda shiny                 # Spinda quests (shiny possible)
    ```

=== "Telegram"

    ```
    /quest spinda shiny
    ```

## Stardust Rewards

=== "Discord"

    ```
    !quest stardust                     # Any stardust reward
    !quest stardust500                  # Stardust rewards >= 500
    !quest stardust1000                 # Stardust rewards >= 1000
    ```

=== "Telegram"

    ```
    /quest stardust
    /quest stardust500
    ```

## Mega Energy Rewards

=== "Discord"

    ```
    !quest energy                       # All mega energy rewards
    !quest energycharizard              # Charizard mega energy specifically
    !quest energyblastoise              # Blastoise mega energy
    ```

=== "Telegram"

    ```
    /quest energy
    /quest energycharizard
    ```

## Candy Rewards

=== "Discord"

    ```
    !quest candy                        # All candy rewards
    !quest candypikachu                 # Pikachu candy specifically
    ```

=== "Telegram"

    ```
    /quest candy
    /quest candypikachu
    ```

## Item Rewards

Track specific item rewards:

=== "Discord"

    ```
    !quest rare candy                   # Rare candy rewards
    !quest silver pinap berry           # Silver Pinap Berry rewards
    ```

=== "Telegram"

    ```
    /quest rare candy
    ```

## All Quest Options

| Option | Description | Example |
|--------|-------------|---------|
| `<pokemon>` | Quest rewarding pokemon encounter | `spinda` |
| `stardust` | Any stardust reward | `stardust` |
| `stardust<n>` | Stardust reward >= amount | `stardust500` |
| `energy` | All mega energy rewards | `energy` |
| `energy<pokemon>` | Specific mega energy | `energycharizard` |
| `candy` | All candy rewards | `candy` |
| `candy<pokemon>` | Specific candy | `candypikachu` |
| `<item_name>` | Specific item reward | `rare candy` |
| `shiny` | Shiny-eligible encounters | `shiny` |
| `d<n>` | Distance in meters | `d500` |
| `template<n>` | Alert template | `template2` |
| `clean` | Auto-delete expired alerts | `clean` |
| `summary` | Buffer matches for grouped delivery on a schedule | `summary` |
| `everything` | All quest rewards | `everything` |
| `remove` | Remove tracking | `remove` |

## Buffered Summary Delivery

Instead of receiving a separate message for every matched quest, you can opt a rule into **summary delivery**: matched quests are held in memory and dispatched as grouped messages on a schedule you set (e.g. one digest at 07:30 each weekday morning, grouped by reward).

Add `summary` to any `!quest` rule:

=== "Discord"

    ```
    !quest pikachu summary              # Pikachu quests, summary delivery
    !quest rare candy summary           # Rare Candy quests, summary delivery
    !quest stardust1000 summary         # Stardust ≥ 1000, summary delivery
    ```

=== "Telegram"

    ```
    /quest pikachu summary
    /quest rare candy summary
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
    !quest remove everything            # Remove all quest tracking
    ```

=== "Telegram"

    ```
    /quest remove spinda
    /quest remove everything
    ```
