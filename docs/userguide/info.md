# Info & Lookups

`!info` looks up pokemon, items, and current game state. No tracking required — these are read-only queries you can run any time to plan your tracking rules.

## Pokemon Details

=== "Discord"

    ```
    !info pikachu                       # By name
    !info 25                            # ...or by dex ID
    !info charizard
    ```

=== "Telegram"

    ```
    /info pikachu
    /info 25
    ```

Returns types, base stats, generation, evolution chain, available forms, weather boost, and the 100% IV CP at level 40.

## Moves

=== "Discord"

    ```
    !info moves                         # List every move + ID
    ```

=== "Telegram"

    ```
    /info moves
    ```

Useful for finding the right `move:` token when tracking — e.g. `!raid mewtwo move:psystrike`.

## Items

=== "Discord"

    ```
    !info items                         # List every item the bot recognises
    ```

=== "Telegram"

    ```
    /info items
    ```

The list it prints is the **exact token to use** in `!quest <item>` (replacing spaces with underscores: `golden_razz_berry`, `silver_pinap_berry`).

## Current Weather

=== "Discord"

    ```
    !info weather                       # Current in-game weather + which types get boosted
    ```

=== "Telegram"

    ```
    /info weather
    ```

Reads the latest weather cells from your scanner, and lists which pokemon types are currently weather-boosted.

## Available Templates

=== "Discord"

    ```
    !info templates                     # DTS template numbers loaded on this server
    ```

=== "Telegram"

    ```
    /info templates
    ```

The list tells you which `template:N` values your tracking rules can use — e.g. if `!info templates` shows `1, 2, dark`, then `!track pikachu template:dark` is valid.

## Rarity Tiers

=== "Discord"

    ```
    !info rarity                        # Per-tier breakdown: common → ultra-rare
    ```

=== "Telegram"

    ```
    /info rarity
    ```

Lists which species are in each rarity tier. Pair with `rarity:` filters — e.g. `!track everything rarity:4-5` to track everything Very Rare or Ultra Rare.

## Shiny Rates

=== "Discord"

    ```
    !info shiny                         # Observed shiny rates per species
    ```

=== "Telegram"

    ```
    /info shiny
    ```

Sample-based and server-specific — the rates reflect what *your* server's scanner has observed, not in-game baseline rates.

## Related

- [Pokemon Tracking](pokemon.md) — `!track` uses everything `!info` returns.
- [Quests](quests.md) — `!info items` is the canonical list for `!quest <item>` tokens.
- [`!tracked`](commands.md#registration-status) — see your active tracking rules.
