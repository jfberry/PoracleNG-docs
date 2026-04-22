# Scanner Setup

PoracleNG receives Pokemon Go data via webhooks from a scanner. **[Golbat](https://github.com/UnownHash/Golbat)** is the main and recommended source. **RDM** is supported for setups that already use it.

!!! warning "MAD Not Supported"
    MAD scanner support has been removed in PoracleNG. If you currently use MAD, you'll need to switch to Golbat.

## Golbat (recommended)

[Golbat](https://github.com/UnownHash/Golbat) is the default scanner and where most webhook traffic for PoracleNG comes from. Webhooks are configured in Golbat's own `config.toml`, not in PoracleNG — see Golbat's example config in its repository for the full set of options (filters by `types`, `areas`, custom `headers`, multiple endpoints, etc.).

All you need to tell Golbat is where PoracleNG is listening:

```toml
# In Golbat's config.toml
[[webhooks]]
url = "http://<poracle-host>:3030/"
```

The webhook endpoint is the processor's **root path `/` on port 3030** (or whatever you've set in PoracleNG's `[processor]` host/port).

### Webhook types PoracleNG processes

Golbat can filter which webhook types it sends. PoracleNG processes these:

| Type | Description |
|------|-------------|
| `pokemon` | All pokemon (with and without IVs) |
| `pokemon_iv` | Only encountered pokemon with IV/CP/level data |
| `pokemon_no_iv` | Only nearby pokemon without encounter data |
| `raid` | Raids and eggs |
| `quest` | Field research quests |
| `invasion` | Team Rocket invasions |
| `pokestop` | Pokestop updates (lures) |
| `gym` | Gym team/details changes |
| `weather` | In-game weather changes |
| `fort_update` | Fort (gym/pokestop) metadata changes |

By default Golbat sends all of these; restrict with a `types = [...]` filter in the `[[webhooks]]` entry if you want to narrow the stream. Refer to Golbat's docs for the exact syntax and any additional filters they've added.

## RDM

If your webhook source is RDM, point its webhook configuration at the same endpoint:

```
http://<poracle-host>:3030/
```

To enable RDM-specific features on the PoracleNG side (pokestop name lookups, some quest enrichment), also configure the scanner database connection and set the type:

```toml
[database.scanner]
host = "127.0.0.1"
port = 3306
user = "scanneruser"
password = "scannerpassword"
database = "scannerdb"
type = "rdm"          # "golbat" (default) or "rdm"
```

!!! note "Back-compat alias"
    Older configs set `scanner_type` under `[database]` instead. PoracleNG still reads that spelling for backward compatibility, but the canonical location is `[database.scanner].type`.

## Verifying Webhooks

After configuring your scanner, verify that the processor is receiving webhooks:

```sh
# Check processor health
curl http://localhost:3030/health

# Check processor metrics for webhook counts
curl http://localhost:3030/metrics | grep webhook
```

You can also check `logs/processor.log` for incoming webhook activity.

## Disabling Webhook Types

If you want to disable processing of specific webhook types on the PoracleNG side (regardless of what the scanner sends), use the `disable_*` settings in `config.toml`:

```toml
[general]
disable_pokemon = false
disable_raid = false
disable_pokestop = false
disable_invasion = false
disable_lure = false
disable_quest = false
disable_weather = false
disable_nest = false
disable_gym = false
disable_fort_update = false
```
