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
| `everything` | All quest rewards | `everything` |
| `remove` | Remove tracking | `remove` |

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
