# User Guide

Welcome to the PoracleNG user guide. This section explains how to set up and use tracking as an end user.

## Getting Started

1. **[Register](registration.md)** — register with the bot on Discord or Telegram
2. **[Set your location](areas.md)** — add areas or set a location for tracking
3. **Start tracking** — subscribe to alerts for what you want:
   - [Pokemon](pokemon.md) — wild spawns, IV filters, level, rarity
   - [Raids & Eggs](raids.md) — raid bosses and eggs by tier
   - [PVP](pvp.md) — Great/Ultra/Little League rank tracking
   - [Quests](quests.md) — field research rewards
   - [Invasions](invasions.md) — Team Rocket grunts and leaders
   - [Lures](lures.md) — lure modules on pokestops
   - [Nests](nests.md) — nest pokemon
   - [Gyms](gyms.md) — gym team changes, EX raids

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
- Use `!tracked` to see all your current tracking subscriptions
- Use `!stop` to temporarily pause all alerts, `!start` to resume
- Most tracking commands support the `remove` keyword to delete a subscription
- Commands are case-insensitive
