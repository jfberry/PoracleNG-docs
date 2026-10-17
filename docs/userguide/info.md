# Info & Lookups

`!info` looks up pokemon, items, and current game state. No tracking required — these are read-only queries you can run any time to plan your tracking rules.

## Pokemon Details

=== "Discord"

    ```
    !info pikachu                       # By name
    !info 25                            # ...or by dex ID
    !info vulpix form:alola             # A specific form
    !info pikachu forms                 # Full form list
    !info pikachu costumes              # Costumes recently seen on pikachu
    ```

=== "Telegram"

    ```
    /info pikachu
    /info 25
    /info vulpix form:alola
    /info pikachu forms
    ```

Returns base stats, generation, types (with weather boost and type matchups), evolution chain, and the 100% IV CP at levels 15, 20, 25, 40, 50 and 51. Where your server has data it also shows the observed shiny rate and these sections:

| Section | What it lists |
|---------|---------------|
| Recently-seen forms | Forms of this species seen in recent spawns |
| Recently-seen raid forms | Forms seen recently as raid bosses |
| Recently-seen costumes | Costumes seen recently on spawns |
| Recently-seen raid costumes | Costumes seen recently on raid bosses |
| Available forms | Every form in the game data (first 10; use `!info <pokemon> forms` for all) |

Each entry is printed as a ready-to-paste tracking token — e.g. `vulpix form:alola` or `pikachu costume:holiday_2016` — so you can copy it straight into `!track` or `!raid`.

If your language is not English, the translated form prefix works in `!info <pokemon> form:…` as well as `form:` (e.g. French `forme:`) in 5.3+.

`!info <pokemon> forms` shows the recent and full form lists untruncated; `!info <pokemon> costumes` shows every costume recently seen on that species (spawns and raids combined).

## Costumes

=== "Discord"

    ```
    !info costumes                      # Every known costume: id — name
    ```

=== "Telegram"

    ```
    /info costumes
    ```

Use the id or name with `costume:` — e.g. `!track pikachu costume:5`. Costume `0` (no costume) isn't listed but can be used as `costume:0`.

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
    !info weather                       # Weather at your location
    !info weather 51.50,-0.12           # Weather at specific coordinates
    ```

=== "Telegram"

    ```
    /info weather
    /info weather 51.50,-0.12
    ```

Shows the current in-game weather for the S2 weather cell at your `!location` (or the coordinates you give), the types it boosts, and the forecast for the next hour when available.

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

## Admin Lookups

Admins also have:

| Command | Description |
|---------|-------------|
| `!info dts` | Summary of loaded DTS templates by type and platform |
| `!info translate <word>` | Debug a translation — forward/reverse key lookup for a word |

`!info poracle` and `!info config` have moved to `!poracle-admin status` and `!poracle-admin config`; the old forms just print a pointer to the new command.

## Related

- [Pokemon Tracking](pokemon.md) — `!track` uses everything `!info` returns.
- [Quests](quests.md) — `!info items` is the canonical list for `!quest <item>` tokens.
- [`!tracked`](commands.md#registration-status) — see your active tracking rules.
