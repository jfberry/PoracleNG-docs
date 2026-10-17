# Delivery, test & commands

Endpoints for sending messages through Poracle's delivery queue, generating test alerts, running bot commands over HTTP, resolving Discord/Telegram ids to names, and inspecting delivered-message snapshots. All need `X-Poracle-Secret`.

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/api/deliverMessages` | Queue pre-rendered messages for delivery |
| `POST` | `/api/postMessage` | Legacy alias of `/api/deliverMessages` |
| `POST` | `/api/test` | Render a webhook and deliver it to one target |
| `POST` | `/api/command` | Run a bot command and get the replies back |
| `POST` | `/api/resolve` | Look up names for Discord/Telegram ids |
| `GET` | `/api/snapshots/{messageID}?target=` | Inspect the snapshot stored for a delivered alert |

## Deliver pre-rendered messages

`POST /api/deliverMessages` puts already-rendered messages onto Poracle's delivery queue, so they go out with the same rate limiting, retries and clean-up as alerts. `POST /api/postMessage` is identical and kept for older clients.

The body is always an **array** of jobs:

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  http://localhost:3030/api/deliverMessages \
  -d '[{
        "target": "123456789012345678",
        "type": "discord:user",
        "name": "Alice",
        "message": {"content": "Raid night starts at 7pm!"},
        "tth": {"hours": 1},
        "clean": 1
      }]'
```

```json
{"status": "ok", "queued": 1}
```

| Field | Notes |
|---|---|
| `target`* | Discord user/channel/thread id, webhook URL, or Telegram chat id |
| `type`* | `discord:user`, `discord:channel`, `discord:thread`, `webhook`, `telegram:user`, `telegram:group`, `telegram:channel` |
| `message`* | The platform payload, e.g. `{"content": "…", "embeds": [...]}` for Discord |
| `name` | Destination name for logs |
| `tth` | `{days, hours, minutes, seconds}` — when the message expires (used with `clean`) |
| `clean` | Bitmask: `1` delete at `tth`, `2` track for in-place edits, `3` both |
| `editKey`, `replyKey`, `msgType`, `logReference`, `lat`, `lon` | Advanced: edit tracking, reply threading, lifecycle type, log tracing |

Unknown fields are rejected with `422`. Jobs missing `target` or `type` are skipped (they don't count towards `queued`). `503` means the delivery dispatcher isn't running.

## Test alerts

`POST /api/test` takes a raw scanner webhook, enriches and renders it with the target's template and language, and delivers it to that target — skipping tracking-rule matching. It's what `!poracle-test` uses.

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  http://localhost:3030/api/test \
  -d '{
        "type": "pokemon",
        "target": {"id": "123456789012345678", "type": "discord:user", "name": "Alice", "language": "en", "template": "1",
                   "latitude": 51.28, "longitude": 1.08},
        "webhook": {"pokemon_id": 149, "latitude": 51.2805, "longitude": 1.0792, "disappear_time": 9999999999,
                    "individual_attack": 15, "individual_defense": 15, "individual_stamina": 15}
      }'
```

- `type` is a webhook type (`pokemon`, `raid`, `quest`, `pokestop`, `gym`, `nest`, `fort_update`, `max_battle`, …) or a DTS type name (`monster`, `monsterChanged`, `incident`, …); hyphenated forms such as `fort-update` also work.
- `type`, `webhook` and `target.id` are required (`400` otherwise). `target.latitude`/`longitude` are the "user location" used for distance and directions.
- For ready-made webhooks, use the scenarios from [`GET /api/dts/testdata`](dts.md#get-apidtstestdata).

Returns `{"status": "ok"}` once the message is queued.

## Run a bot command

`POST /api/command` runs a command line through the same parser and handlers as the Discord and Telegram bots, as if the given user had typed it, and returns the replies instead of sending them.

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  http://localhost:3030/api/command \
  -d '{"text": "!tracked", "user_id": "123456789012345678", "user_name": "Alice", "platform": "discord", "is_dm": true}'
```

```json
{"status": "ok", "replies": [{"text": "Your tracking for profile 1: …"}]}
```

| Field | Notes |
|---|---|
| `text`* | The full command line **including the prefix** — the Discord `[discord] prefix` (default `!`). Several commands can be sent on separate lines. Text without the prefix is ignored and returns no replies. |
| `user_id`* | Who is running it. Their language, active profile and permissions apply. |
| `platform` | `discord` or `telegram`; used for admin and `command_security` checks |
| `user_name`, `channel_id`, `guild_id`, `is_dm` | Context, as a bot command would have |

Each reply may have `text`, `embed`, `react` (e.g. `🙅` when the command isn't permitted), `imageUrl`, `attachment` and `isDM`. Admin targeting such as `user<id>` and `name<webhook>` works as in the bot. Commands with side effects (tracking changes, `!stop`, …) really happen.

## Resolve ids to names

`POST /api/resolve` turns ids into display names in one call — used by the config editor to label admin lists and destinations. Results are cached for 10 minutes. Ids that can't be resolved are left out; a platform's section is omitted when that bot isn't running.

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  http://localhost:3030/api/resolve \
  -d '{"discord": {"users": ["344179542874914817"], "roles": ["987654321"], "channels": ["111222333"], "guilds": ["444555666"]},
       "telegram": {"chats": ["-100123456"]},
       "destinations": ["111222333", "raid-feed"]}'
```

```json
{
  "status": "ok",
  "discord": {
    "users": {"344179542874914817": {"name": "jamesberry", "globalName": "James Berry"}},
    "roles": {"987654321": {"name": "Moderator", "guild": "My Server", "guildId": "444555666"}},
    "channels": {"111222333": {"name": "raid-alerts", "type": "text", "guild": "My Server", "guildId": "444555666", "categoryName": "Pokemon"}},
    "guilds": {"444555666": {"name": "My Server"}}
  },
  "telegram": {"chats": {"-100123456": {"name": "Pokemon Group", "type": "supergroup"}}},
  "destinations": {
    "111222333": {"kind": "discord:channel", "name": "raid-alerts", "enabled": true, "notes": "EU raids", "areas": ["london"]},
    "raid-feed": {"kind": "webhook", "name": "raid-feed", "enabled": true}
  }
}
```

`destinations` is for ids of unknown type (config fields with `resolve: "destination"`). Poracle checks registered humans first, then Discord (channel, user, role, guild), then Telegram. `kind` says what matched. `"stale": true` means the id is registered in Poracle but the platform can no longer find it (e.g. a deleted channel) — warn before keeping it.

## Message snapshots

When `[snapshots] enabled = true`, Poracle stores the enrichment data behind each delivered alert so [alert buttons](../configuration/dts.md) can act on it later. `GET /api/snapshots/{messageID}?target={destinationId}` returns one for diagnostics:

```sh
curl -H "X-Poracle-Secret: your-secret" \
  "http://localhost:3030/api/snapshots/1294012345678901234?target=111222333444555666"
```

The snapshot includes `messageId`, `target`, `targetType`, `createdAt`, `expiresAt`, `alertType`, `templateType` and the stored render data. `404` if it has expired or never existed; `503` when snapshots are disabled.
