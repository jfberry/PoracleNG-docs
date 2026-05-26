# User Guide

Welcome to the PoracleNG user guide. This section explains how to set up and use tracking as an end user.

## Getting Started

1. **[Register](registration.md)** — register with the bot on Discord or Telegram
2. **[Set your areas and location](areas.md)** — add areas, set a default location, and optionally save named locations for per-rule overrides
3. **Start tracking** — subscribe to alerts for what you want:
   - [Pokemon](pokemon.md) — wild spawns, IV filters, level, rarity
   - [Raids & Eggs](raids.md) — raid bosses and eggs by tier
   - [PVP](pvp.md) — Great/Ultra/Little League rank tracking
   - [Quests](quests.md) — field research rewards (with optional summary delivery)
   - [Invasions](invasions.md) — Team Rocket grunts and leaders, Kecleon, Gold Pokéstops
   - [Lures](lures.md) — lure modules on pokestops
   - [Nests](nests.md) — nest pokemon
   - [Gyms & Forts](gyms.md) — gym team changes, EX raids, fort updates
   - [MAX Battles](maxbattle.md) — Dynamax / Gigantamax raids
4. **Refine** — [mute](mute.md) noisy alerts, set up [profiles](profiles.md) to switch tracking configurations, or use [`!info`](info.md) to look up pokemon and game data
5. **(Discord)** — if your operator has enabled them, the same commands are available as [slash commands](slash.md) with autocomplete

## Command Syntax

=== "Discord"

    Commands use the `!` prefix (configurable by admin):
    ```
    !track pikachu iv90
    !area add downtown
    !tracked
    ```

=== "Telegram"

    Commands use the `/` prefix:
    ```
    /track pikachu iv90
    /area add downtown
    /tracked
    ```

Throughout this guide, both Discord (`!`) and Telegram (`/`) syntax are shown.

## Quick Reference

See the [Command Reference](commands.md) for a complete table of all commands and options.

## Profiles

Use [Profiles](profiles.md) to save and switch between different tracking configurations (e.g. home vs office).

## Tips

- You must set at least one area or location before you'll receive alerts
- Use `!tracked` to see all your current tracking subscriptions and active mutes
- Use `!stop` to indefinitely pause all alerts, `!start` to resume; for time-bounded silence use [`!mute`](mute.md) instead
- Most tracking commands support the `remove` keyword to delete a subscription
- Filter options use `key:value` form — `iv:90-100`, `cp:2000-3000`, `d:500`. Ranges (`low-high`) are preferred over `miniv:`/`maxiv:` pairs; bare-minimum-with-colon (`iv:99`) is fine when you only want a floor
- Commands are case-insensitive
