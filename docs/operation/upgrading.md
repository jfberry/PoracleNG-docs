# Upgrading & Release Notes

This page covers what changes between PoracleNG releases and what to check when you upgrade. For moving from PoracleJS, see the [Migration Guide](../migration/guide.md).

| Branch | Version | Go (source builds) | Docker tag |
|--------|---------|--------------------|------------|
| `main` | 5.2.x (current: 5.2.1) | 1.25+ | `ghcr.io/jfberry/poracleng:main` / `:latest` |
| `develop` | 5.3.0-dev | 1.27+ | `ghcr.io/jfberry/poracleng:develop` |

`!version` shows the version you are running, with the branch, commit and build date.

## Before Every Upgrade

!!! danger "Back up your database"
    Pending migrations run automatically when the processor starts, and there is no downgrade command. An older build will refuse to start against a schema migrated by a newer one, and some migrations rewrite data. Take a backup first:

    ```sh
    mysqldump --single-transaction poracle > poracle-backup-$(date +%F).sql
    ```

    For Docker, see [Docker → Updating](../installation/docker.md#updating).

Then:

1. Read the notes below for every version between yours and the target.
2. Update: `git pull && make build`, then restart. Or for Docker: `docker compose pull && docker compose up -d`.
3. Check the startup log for `Database migrated to version N` (or `Database schema is up to date`) and for any `WARN` lines.
4. Run `!poracle-admin warnings startup` to list warnings raised during startup.

---

## 5.3.0 (develop) { #530-develop }

!!! info "PoracleNG 5.3+"
    Everything in this section is only on the `develop` branch / `:develop` image so far.

### Breaking / Action Required

#### Go 1.27

Building from source now needs **Go 1.27**. The Docker image is unaffected.

#### Migration 000009: profile 0 → profile 1

Earlier versions created new humans on **profile 0**, through `!poracle`, Discord role reconciliation, `!channel add`, `!webhook add` and Telegram channel registration. Profile 0 had no `profiles` row, but the default profile is 1. Bot commands still worked, because they saved to the current profile (0). Anything that addressed profile 1 directly, such as PoracleWeb, saved rules that never matched. A user who switched to profile 2 could never switch back to their profile-0 rules.

New humans now start on profile 1, and migration 000009 repairs existing data:

- Humans **currently on profile 0**: their live area/location is copied onto profile 1 (which is created if missing), their profile-0 rules are moved to profile 1, and they are switched to profile 1.
- Humans who **switched away** from profile 0 and whose profile 1 has **no rules**: the profile-0 rules move to profile 1, restoring their lost default profile.
- Humans who **switched away** and **had since rebuilt profile 1**: the profile-0 rules are **deleted** as abandoned. They hadn't fired since the switch and the user couldn't see them.
- Profile-0 rules identical to an existing profile-1 rule are dropped as duplicates.
- Humans with a genuine `profiles` row for profile 0 are left untouched.

!!! warning "Irreversible"
    The down migration is a no-op, and no record is kept of which rows were on profile 0. The only way back is your backup.

#### Delivery: oldest alert dropped on a full lane

When a destination's lane (`[tuning] delivery_queue_size`) is full, the **oldest** queued alert is now dropped to make room. In 5.2, the producer waited instead, and one stuck destination could stall every render worker. Watch for `delivery: lane full … shedding oldest queued messages` and the `[Status] delivery shedding messages` warning, and alert on `poracle_delivery_lane_shed_total`. See [Rate Limits → Delivery Lanes](ratelimits.md#delivery-lanes).

#### Rate-limit bans fire once

An auto-disable ban now fires once per 24-hour violation window, instead of repeating the farewell, shame post and admin notice every `timing_period` while a backlog drains. Re-enabling a destination with `!start` or `!enable` clears its breach history, so a repeat offender is banned again. See [Rate Limits](ratelimits.md#who-can-be-banned).

#### Behind a reverse proxy: set `trusted_proxies`

`X-Forwarded-For` / `X-Real-IP` are now only believed from addresses listed in the new `[processor] trusted_proxies` (IPs or CIDRs; empty by default). Before, any client could set those headers and get past `ip_whitelist` / `ip_blacklist`. **If PoracleNG sits behind nginx, Traefik or a Docker network and you use the IP lists, add the proxy**, e.g. `trusted_proxies = ["172.16.0.0/12"]`, or every request is seen as coming from the proxy. The IP lists now also accept CIDR ranges, and an invalid entry in any of the three lists **stops startup**. See [`[processor]`](../config/processor.md).

#### `/debug/pprof` is off by default

Go profiling is no longer served unauthenticated. It only exists with `[processor] enable_pprof = true`, and then needs the `X-Poracle-Secret` header like `/api`. Startup also warns when `api_secret` is blank and the API listens on a non-loopback host (including Docker's `0.0.0.0`).

#### `[discord]` / `[telegram]` `enabled = false` stops alerts

`enabled = false` now stops that platform's alerts (Discord webhooks count as Discord), not just its commands. `config.example.toml` ships `[telegram] enabled = false`: **if you added a Telegram token and left that line, Telegram alerts stop after upgrading.** Set `enabled = true` or delete the line. Startup warns when a platform has a token but is disabled.

#### `!restore` replaces all tracking

`!restore` now clears **every** tracking type on the target's current profile and refills it from the backup in one transaction, so types missing from the backup end up empty. Before, it only replaced the types present in the file. Table and column names in the file are validated first. See [Backup & Restore](admincommands.md#backup-restore).

#### API v2 response shapes and stable rule uids

API v2 clients need updating: humans and profiles now use real booleans and arrays instead of the v1 `0`/`1` flags and JSON-encoded strings, `language` is `""` when unset, location bodies and `check-location` use `latitude`/`longitude` instead of `lat`/`lon`, and the snapshot's `summaries[]` loses `id`. `PUT …/tracking/{type}/{uid}` now keeps the rule's `uid`, and editing a rule through a bot command, v1 or v2 `POST` keeps its `uid` too, so `id:N` references, rule mutes and alert buttons survive edits. v1 is unchanged. See [API v2](../api/v2/index.md#response-shapes) and [v2 tracking](../api/v2/tracking.md).

#### Other behaviour changes

- **Commands are stricter.** Arguments that used to be accepted and ignored are now reported: `!track … shiny`, an unknown `move:` on `!raid` / `!maxbattle` (it used to mean "any move"), and `gen`/type names on `!raid`. `!maxbattle` with a gen or type but no pokemon is refused. An unrecognised word on `!invasion remove` / `!incident remove` is rejected; it used to delete **every** invasion rule.
- **`!quest` with several rewards** now creates a rule for each reward named, not just the first.
- **`user` target override** must be numeric (`user:123…`, `user-100…`). Words that merely start with "user" are no longer taken as an override.
- **Admins bypass `[discord.command_security]`** on text commands and `POST /api/command` too, not just slash commands.
- **Delegated admins work:** Discord category targets (and a thread's parent channel), `[[telegram.delegated_admins]]`, and Telegram community admins managing their groups and running `/userlist`. See [Discord](discord.md#delegated-admins) and [Telegram](telegram.md#delegated-admins-and-tracking-admins).
- **`!enable`, `!disable`, `!unregister`** list unregistered IDs as "Not registered" instead of reporting success.
- **Rarity:** `rarity:6` (Unseen) now matches species with no sightings in the window, so rules with a minimum rarity (e.g. `rarity:rare`) also fire for unseen species.
- **Templates:** a user's language-specific default template is now chosen over the language-less default. `previousControlTeamEmoji` shows the previous controller's team (it showed the old team), and maxbattle `{{id}}` is the boss pokemon ID instead of the station ID. See [DTS fields](../configuration/dtsfields.md).
- **`!weather` removed.** It always replied "unavailable". `!info weather` is unaffected.
- **Slash commands re-register** on the next start, because their definitions changed (`/fort` loses `clean` and gains Station/Everything; `/profile copyto` accepts the autocompleted number).
- **`config.example.toml` cleaned up:** keys the code never read are gone, and `filter_max_rank` now shows the real default `100` (was `10`). If you copied the old example, nothing changes, but you can delete `environment`, `worker_status`, `worker_activity`, `sprite_*`, `scale`, `encounter_cache_ttl`, `batch_size`, `flush_interval_millis` and the old alerter tuning keys. Leaving out `thread_keep_alive_interval_hours` or `removal_safety_max_percent` means `0` (off), not the example's value; the Config UI no longer drops them when saved.

### New

- **Pokemon `edit`.** `!track … edit` edits the original alert in place when the pokemon changes, instead of sending a threaded follow-up. New `[tracking]` keys: `pokemon_edit` (default `true`) and `pokemon_edit_window_mins` (default `5`, `0` = no limit). Slash `/track`, `/raid`, `/egg` and `/lure` gain an `edit` option.
- **`[tracking] default_edit`.** A list of alert types that behave as if every rule had `edit`. Supported types are `pokemon`, `raid`, `egg`, `lure` and `incident`.
- **PVP `level_caps` mismatch warning.** If Golbat sends ranks for a level cap that isn't in `[pvp] level_caps`, a `PVP configuration mismatch` warning is logged once per cap. Those ranks were always ignored; now you are told.
- **Unsubscribe button** now works on pokemon and fort-update alerts.
- **PoracleJS DTS compatibility** improvements: custom maps (`{{map}}` / `{{map2}}`), evolution fields, extra string helpers, and `GET /api/dts/custommaps` for the DTS editor.
- **API:** v2 tracking accepts `ping`, `DELETE /api/v2/humans/{id}/location` clears a location, v2 clamps distances to `[tracking] max_distance`, and there is a new `GET /api/geocode/reverse`.
- `!poracle-admin config` output is now in code blocks, which fixes mangled `snake_case` keys on Telegram.
- **`!poracle-test nest riverside`:** bundled nest test data. With `location: "current"` the nest (and its polygon) moves to the tester.
- **`!script`** now exports `!maxbattle` and `!fort` rules.
- **`POST /api/command`** adds the command prefix to lines that lack it, so `track pikachu` works as well as `!track pikachu`.
- **`!pa config`** section names and search terms ignore case and treat `_` and spaces alike (`!pa config alert_limits`).
- **`!nest everything gen1`** / `everything fire` apply the gen/type filter instead of ignoring it.
- **`!info <pokemon> form:`** also accepts the translated prefix (e.g. French `forme:`).

### Fixes worth knowing

- PVP rules stored with `pvp_ranking_worst = 0` now match (`0` is treated as "no limit").
- Re-running a tracking command with only a different `ping` updates the rule instead of adding a copy.
- Weather-change alerts are no longer dropped when Golbat re-sends quickly. A species change that keeps the same CP (e.g. a Ditto reveal) now produces a change alert.
- `{{latitude}}` / `{{longitude}}` now render in fort-update templates.
- The v2 `?profile=` on location/areas no longer overwrites the active profile's live location.
- `!channel add area:<name_with_underscores>` and `name:` values with underscores are no longer silently dropped.
- `!lure remove id:N` (and so `/untrack lure`) and `!maxbattle remove level:N` now remove rules; `!unmute area` is case-insensitive; per-type mute aliases (`!raid mute gym X`) work.
- `rarity:very-rare` / `ultra-rare` parse.
- Trace-level logging no longer crashes the processor; `{{#pokemon id}}` without a form no longer panics; help and greeting templates render in the user's language and can't crash the bot.
- `{{ex}}`, `oldTeamEmoji` and `showcaseFocusEmoji` are populated.
- Alerts that fall back say why: "Template not found", "Template render error" or "Template produced invalid JSON".
- `!pa maintenance` replies now say paused alerts are dropped (they never were queued). `examples/broadcast.json` is valid JSON, and the `!broadcast` help matches the command.
- `monitoring/prometheus.yml.example` no longer scrapes the removed alerter on port 3031.

---

## 5.2.x (main) { #52x-main }

### Breaking / Action Required

#### Logging level names

`[logging] level` now accepts `trace`, `debug`, `info` and `warn` (default `info`). The PoracleJS names are aliases: `verbose` means `info` and **`silly` means `trace`**. In 5.1 and earlier, neither alias was recognised and both fell back to `info`. A config left on `silly` therefore goes from info-level output to the most verbose level after upgrading. Set `level = "info"` unless you want that. Legacy `log_level` / `console_log_level` keys are folded into `level`.

#### `disable_pokestop` is a no-op

`[general] disable_pokestop` is still accepted but does nothing. Use `disable_lure`, `disable_invasion`, `disable_quest`, and the new `disable_showcase`. See [Scanner Setup](../installation/scanner.md#disabling-webhook-types).

#### `delivery_queue_size` is now per destination

Delivery moved to **per-destination lanes**. `[tuning] delivery_queue_size` (default 200) is now the buffer for **each** active destination, not one shared queue, so memory under a backlog grows with the number of destinations. If you raised it a lot for the old shared queue, bring it back toward the default. Idle lanes are removed after 60 seconds. New `poracle_delivery_lane_*` metrics are listed on the [Monitoring](monitoring.md#delivery-lane-metrics) page.

#### Migrations 000006–000008

These add a `costume` column to `monsters` and `raid`, and drop the legacy `invasion_tracking` / `lure_tracking` unique keys. They are applied automatically.

### New

- **Showcases.** Pokestop contests are now alerts, driven by the `pokestop` webhook, with their own `showcase` DTS template and `disable_showcase`.
- **Costume tracking.** `costume:<name|id>` works on `!track` and `!raid`.
- **Quest pokecoin rewards** (`pokecoins`).
- **OpenAPI and `/api/v2`.** `GET /openapi.json` and interactive docs at `/docs`. v1 tracking/humans/profiles endpoints are frozen and deprecated. Error bodies are now RFC 9457 `problem+json`. Includes a v2 mutes API.
- **Geocoding:** GeoNames `intersection_users` for `{{intersection}}`, and shared circuit breakers on external services.
- Delivery lanes, quest wildcard fixes, lure multi-type fixes, and channel auto-create no longer moves a same-named channel out of another category.

---

## 5.1.0 { #510 }

- **Version reporting.** `!version` shows the commit, branch and build date.
- **PVP mega evolutions.** `mega`, `mega:x` and `mega:y` keywords on PVP rules (migration 000005 adds `pvp_ranking_evolution`). `[pvp] include_mega_evolution` changed meaning: it is now only the **default for rules without a `mega` keyword** (`false` = base forms only; `true` = include mega ranks). A per-rule keyword always overrides it.
- **Per-rule location/area overrides**, quest summaries, Photon geocoding, and the Rampardos static map provider.

### PoracleJS migration script fixes

If you converted a PoracleJS config **before 5.1.0**, check two things the old `scripts/migrate-from-poracle.js` got wrong:

- **`[general.dts_dictionary]`** — custom keys were snake-cased (`scannerUrl` → `scanner_url`), so templates using `{{scannerUrl}}` render empty. Rename the keys back to exactly what your templates use.
- **`[processor] host`** — PoracleJS's `server.host` was dropped. Set it again if you bound the API to a specific address.
