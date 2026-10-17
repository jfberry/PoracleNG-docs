# Validation hook

The validation hook lets **your** service approve or deny each alert recipient. Poracle calls a URL you supply once per matched user, after its own filtering and before it spends effort enriching and rendering the alert. Typical uses are paid tiers and subscription checks, A/B tests, or any custom rule you don't want to patch into Poracle.

This is an outbound call from Poracle to you; there's no Poracle endpoint to call.

## Configuration

```toml
[validation]
url = "https://license.example.com/poracle/validate"
fail_mode = "open"

[tuning]
validation_timeout_ms = 1500
validation_max_concurrent = 16
```

| Setting | Default | Meaning |
|---|---|---|
| `[validation] url` | `""` | Endpoint to POST to. Empty disables the hook completely (no overhead). |
| `[validation] fail_mode` | `"open"` | What to do when your service times out, errors, returns non-2xx, or sends an unreadable body: `"open"` allows the user, `"closed"` denies them. |
| `[tuning] validation_timeout_ms` | `1500` | Timeout per call, in milliseconds |
| `[tuning] validation_max_concurrent` | `16` | Maximum parallel calls for one event |

See also the [`[validation]` config reference](../config/validation.md).

Use `"open"` unless an unvalidated alert is unacceptable — with `"closed"`, an outage of your service stops alerts for everyone.

## Request

```
POST <url>
Content-Type: application/json
```

```json
{
  "human": {"id": "123456789012345678", "type": "discord:user", "name": "Alice"},
  "areas": ["Canterbury", "Kent Coast"],
  "webhook_type": "raid",
  "webhook": {"gym_id": "0a1b2c3d4e.16", "level": 5, "pokemon_id": 150, "...": "..."}
}
```

| Field | Meaning |
|---|---|
| `human.id` | Destination id (Discord user/channel, webhook name, Telegram chat) |
| `human.type` | `discord:user`, `discord:channel`, `webhook`, `telegram:user`, … |
| `human.name` | Name stored for the destination |
| `areas` | Geofence names containing the event, as configured (not lower-cased) |
| `webhook_type` | `pokemon`, `raid`, `egg`, `quest`, `pokestop`, `invasion`, `nest`, `gym`, `fort_update`, `max_battle` |
| `webhook` | The original scanner webhook, unchanged |

## Response

Reply `200` with JSON:

| Body | Effect |
|---|---|
| `{"success": true}` | Allow — the alert proceeds to this user |
| `{"success": false}` | Deny silently |
| `{"success": false, "failure_message": "Your subscription has expired"}` | Deny and send the user that text once. It bypasses their rate limit so they always see it, and is sent verbatim — Poracle doesn't translate it. |

Anything else — timeout, network error, non-2xx status, missing `success` — follows `fail_mode`. Users denied because of `fail_mode = "closed"` get no message.

## Example validator

```python
def handle(req):
    user = lookup_subscription(req["human"]["id"])
    if user.tier == "paid" or req["webhook_type"] == "raid":
        return {"success": True}           # free tier gets raids only
    return {
        "success": False,
        "failure_message": "Pokemon and quest alerts are for supporters — see example.com/poracle",
    }
```

Because this runs for every matched user on every event, keep it fast and cache subscription lookups. Note that a `failure_message` is sent each time an alert is denied, so a free user tracking many pokemon could receive it often; return a silent deny once they've been told.

## Where it runs

For each matched user:

1. Tracking-rule matching
2. Rate-limit pre-filter (users already over their limit are dropped)
3. Type-specific filters (e.g. raid RSVP preferences)
4. **Validation hook**
5. Enrichment, rendering and delivery

## Metrics

| Metric | Meaning |
|---|---|
| `poracle_processor_validation_total{result="allow"}` | Allowed by your service |
| `poracle_processor_validation_total{result="deny"}` | Denied by your service |
| `poracle_processor_validation_total{result="error_<reason>"}` | Failed calls: `http_error`, `status_<code>`, `decode_error`, `marshal_error`, `build_error`. These are counted regardless of `fail_mode` and aren't also counted as allow/deny. |
| `poracle_processor_validation_seconds` | Call latency histogram |

See [Monitoring](../operation/monitoring.md) for scraping metrics.
