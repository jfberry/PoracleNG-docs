# Scanner Setup

PoracleNG receives Pokemon Go data via webhooks from a scanner. **Golbat** is the default and recommended scanner. **RDM** is also supported.

!!! warning "MAD Not Supported"
    MAD scanner support has been removed in PoracleNG. If you currently use MAD, you'll need to switch to Golbat.

## Golbat (Recommended)

[Golbat](https://github.com/UnownHash/Golbat) is the default scanner type. Golbat uses a TOML config file (`config.toml`). Add a `[[webhooks]]` section pointing at the **PoracleNG processor**:

```toml
[[webhooks]]
url = "http://<poracle-host>:3030/raw"
```

By default, Golbat sends all webhook types. To limit to specific types, add a `types` filter:

```toml
[[webhooks]]
url = "http://<poracle-host>:3030/raw"
types = ["pokemon", "pokemon_iv", "gym", "invasion", "quest", "pokestop", "raid", "weather", "fort_update"]
```

You can also restrict webhooks to specific areas and add custom headers:

```toml
[[webhooks]]
url = "http://<poracle-host>:3030/raw"
types = ["pokemon_iv", "raid", "quest", "invasion", "pokestop", "weather"]
areas = ["MyArea"]
headers = ["X-Custom-Header:value"]
```

!!! important
    Point webhooks at the **processor** on port **3030**, not the alerter on port 3031. The webhook endpoint is `/raw`.

### Webhook Types

Golbat can send the following webhook types that PoracleNG processes:

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

## RDM

If you use RDM, set the scanner type in your config:

```toml
[database]
scanner_type = "rdm"
```

And configure the scanner database connection:

```toml
[database.scanner]
host = "127.0.0.1"
port = 3306
user = "scanneruser"
password = "scannerpassword"
database = "scannerdb"
```

RDM webhooks should also be pointed at the processor:

```
http://<poracle-host>:3030/
```

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

If you want to disable processing of specific webhook types, use the `disable_*` settings in `config.toml`:

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
