# `[validation]`

Optional external validation hook. When `url` is set, PoracleNG POSTs to your endpoint once for each matched user, after rate-limit checks and before the alert is rendered. Your service answers allow or deny for that user. Use it for subscription/licensing checks, A/B tests, or other custom rules without changing PoracleNG.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `url` | string | `""` | Validator endpoint. Empty = hook off (no overhead). |
| `fail_mode` | string | `"open"` | What to do when the validator times out, errors or returns a non-2xx status. `open`: allow the alert (your validator going down doesn't stop alerts). `closed`: deny it (for hard licensing where unvalidated alerts are unacceptable). |

Timeout and concurrency are set in [`[tuning]`](tuning.md#validation-hook): `validation_timeout_ms` (default `1500`) and `validation_max_concurrent` (default `16`).

```toml
[validation]
url = "http://127.0.0.1:8080/poracle/validate"
fail_mode = "open"
```

## Request

```json
{
  "human": { "id": "123456789012345678", "type": "discord:user", "name": "Alice" },
  "areas": ["SanFrancisco/Downtown"],
  "webhook_type": "raid",
  "webhook": { "gym_id": "...", "level": 5 }
}
```

- `human.type` is one of `discord:user`, `discord:channel`, `discord:webhook`, `telegram:user`, `telegram:channel`, `telegram:group`.
- `areas` are the geofence names containing the event.
- `webhook` is the original scanner webhook, unchanged.

## Response

Return `200 OK` with JSON:

| Body | Effect |
|---|---|
| `{"success": true}` | Allow. |
| `{"success": false}` | Deny silently; the user is dropped from this alert. |
| `{"success": false, "failure_message": "Subscription expired"}` | Deny, and send `failure_message` to the user as-is. This message isn't counted against their rate limit. |

The full protocol is described in the *Validation Hook* section of `API.md` in the PoracleNG repository.
