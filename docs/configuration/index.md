# Configuration

PoracleNG uses a single shared TOML config file at `config/config.toml` for both the processor and alerter. Both components read from the same file, eliminating the need to keep settings in sync.

## Configuration Files

```
config/
  config.toml              # Your configuration (only overrides needed)
  config.example.toml      # Full reference with all defaults and documentation
  geofences/               # Geofence files (GeoJSON or Poracle format)
  dts.json                 # Custom DTS templates (optional)
  ...                      # Other data files (optional)
```

!!! tip
    You only need to include settings you want to override — defaults are built in. See `config/config.example.toml` for the complete reference with documentation for every setting.

## Config Sections

| Section | Description | Page |
|---------|-------------|------|
| `[processor]` | Processor networking | [Config Reference](config_file.md#processor) |
| `[alerter]` | Alerter networking and API | [Config Reference](config_file.md#alerter) |
| `[database]` | Database connections | [Config Reference](config_file.md#database) |
| `[discord]` | Discord bot settings | [Discord](discord.md) |
| `[telegram]` | Telegram bot settings | [Telegram](telegram.md) |
| `[geofence]` | Geofence paths and Koji | [Geofences](geofence.md) |
| `[pvp]` | PVP calculation settings | [Config Reference](config_file.md#pvp) |
| `[weather]` | Weather alerts and forecast | [Weather](weather.md) |
| `[area_security]` | Multi-community access | [Area Security](areasecurity.md) |
| `[locale]` | Date/time/language formats | [Config Reference](config_file.md#locale) |
| `[general]` | General settings | [Config Reference](config_file.md#general) |
| `[geocoding]` | Address/map providers | [Geocoding](geocoding.md) |
| `[tuning]` | Performance tuning | [Config Reference](config_file.md#tuning) |
| `[tracking]` | Tracking restrictions | [Config Reference](config_file.md#tracking) |
| `[logging]` | Log levels and rotation | [Config Reference](config_file.md#logging) |
| `[alert_limits]` | Rate limiting | [Config Reference](config_file.md#alert-limits) |

## Data Files

Some features use JSON data files loaded from `config/` with automatic fallback to bundled defaults:

| File | Purpose | Fallback |
|------|---------|----------|
| `dts.json` | Discord/Telegram message templates | Yes (`fallbacks/dts.json`) |
| `pokemonAlias.json` | Pokemon name aliases for commands | Yes |
| `partials.json` | Handlebars template partials | Yes |
| `testdata.json` | Test webhook data for `!poracle-test` command | Yes |
| `geofences/*.json` | Geofence definitions | Yes |
| `dts/` | Additional DTS files (merged with dts.json) | No |
| `broadcast.json` | Broadcast message templates | No |
| `channelTemplate.json` | Discord channel auto-creation templates | No |
| `customMaps/` | Custom static map definitions | No |
| `emoji.json` | Custom emoji mappings | No |
| `custom.<lang>.json` | Custom locale translations | No |

Files with a fallback use the bundled version from `fallbacks/` if you haven't placed a custom version in `config/`. Files without a fallback are optional features that are disabled when absent.

To customize a file, copy it from `examples/` into `config/` and edit it there.

## Resource Downloads

On startup, the processor automatically downloads game data:

- **Game Master** (pokemon, moves, items, types) from WatWowMap/Masterfile-Generator
- **Invasion lineups** from WatWowMap/event-info
- **Locale translations** from WatWowMap/pogo-translations

These are cached in `resources/` and reused if the download fails.
