# End-User Web UIs

Two community-built web interfaces let end users manage their PoracleNG tracking subscriptions through a browser instead of chat commands. Both are separate projects maintained outside the PoracleNG repositories; PoracleNG itself just exposes the API they talk to.

| Tool | Stack | Repository |
|---|---|---|
| **PoracleWeb** | Node.js / React | [WatWowMap/PoracleWeb](https://github.com/WatWowMap/PoracleWeb) |
| **PoracleWeb.NET** | ASP.NET (C#) | [PGAN-Dev/PoracleWeb.NET](https://github.com/PGAN-Dev/PoracleWeb.NET) |

Pick whichever fits your hosting stack — they're functionally similar, talk to the same PoracleNG API, and neither requires changes to PoracleNG beyond enabling `api_secret`. Both can be asked about in the Poracle community Discord.

!!! note "These are not admin tools"
    PoracleWeb and PoracleWeb.NET are for end users to manage their own tracking rules. To edit `config.toml` or DTS templates, use the [Poracle Config UI](../configuration/config-ui.md) instead.

## What they do

- Browse and select pokemon, raids, quests, invasions, lures, nests, and gyms to track
- Manage areas and location on an interactive map
- Edit tracking filters (IV, level, CP, PVP, etc.) through form fields
- Switch between profiles
- Works for both Discord- and Telegram-registered users

## Compatibility

Both UIs talk directly to the PoracleNG processor on its API port (default `3030`). PoracleNG is a single binary — there is no separate alerter in front of the API.

```
Web UI ──/api──▶ PoracleNG processor (:3030)
```

## Requirements on the PoracleNG side

PoracleNG must be running with `api_secret` set in `[processor]`:

```toml
[processor]
host = "0.0.0.0"
port = 3030
api_secret = "a-long-random-string"
```

The processor must be reachable from wherever the web UI is served. If you want the web UI accessible over the public internet, front the API with a reverse proxy and restrict by IP or auth as appropriate — the `api_secret` alone is not a substitute for transport security.

!!! note "Backward compatibility"
    Older Poracle installs placed `api_secret` under `[alerter]`. PoracleNG still reads that location and copies the value into `[processor]` at load time, but new installs should put it directly under `[processor]`.

## Support

Both projects are community-maintained:

- **Setup and connectivity help** — the Poracle community Discord (link in PoracleNG's `README.md`)
- **Bugs and feature requests** — file against the respective upstream repository

## Next Steps

See the [Installation Guide](install.md) for setting up either option.
