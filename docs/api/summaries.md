# Summaries API

Manage a user's [alert summary](../userguide/summaries.md) schedules — the HTTP counterpart of the `!summary` command. Tracking rules opt into summaries with the `summary` flag (v2) or bit 4 of `clean` (v1); matching alerts are buffered and delivered as one digest when the schedule fires. Only `quest` summaries are supported today.

All endpoints need `X-Poracle-Secret`.

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/summaries/{id}` | Every schedule for the user |
| `GET` | `/api/summaries/{id}/{alertType}` | One schedule (`404` if none) |
| `POST` | `/api/summaries/{id}/{alertType}` | Create or replace a schedule |
| `DELETE` | `/api/summaries/{id}/{alertType}` | Remove a schedule (same as `!summary quest cleartime`) |
| `POST` | `/api/summaries/{id}/{alertType}/trigger` | Send the buffered digest now (same as `!summary quest now`) |

`{alertType}` must be `quest`; anything else is a `400`.

The endpoints work whether or not `[tracking] quest_summary_enabled` is on, so you can prepare schedules before enabling the feature; the flag only controls whether the scheduler runs and rules are routed into the buffer.

## Read

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/summaries/123456789012345678
```

```json
{
  "status": "ok",
  "schedules": [
    {"id": "123456789012345678", "alert_type": "quest", "active_hours": [{"day": 1, "hours": 7, "mins": 30}, {"day": 6, "hours": 9, "mins": 0}]}
  ]
}
```

`GET /api/summaries/{id}/quest` returns `{"status": "ok", "schedule": {...}}`.

## Set

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" -H "Content-Type: application/json" \
  http://localhost:3030/api/summaries/123456789012345678/quest \
  -d '{"active_hours": [{"day": 1, "hours": 7, "mins": 30}, {"day": 6, "hours": 9, "mins": 0, "step": 3, "end_hours": 18, "end_mins": 0}]}'
```

`active_hours` uses the same entries as [profile schedules](v2/profiles.md#active_hours): `day` 1–7 (ISO, **1 = Monday … 7 = Sunday**), `hours`, `mins`, and optionally `step` / `end_hours` / `end_mins` for repeating ranges within a day. For compatibility this endpoint is lenient: `active_hours` may also be a JSON-encoded string, and numbers may be zero-padded strings such as `"07"`. An empty array (or `""`, `"[]"`) clears the schedule.

Returns `{"status": "ok"}` and triggers a reload so the new schedule is picked up straight away.

## Delete and trigger

```sh
curl -X DELETE -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/summaries/123456789012345678/quest

curl -X POST -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/summaries/123456789012345678/quest/trigger
```

Both return `{"status": "ok"}`. Deleting a schedule that doesn't exist is not an error. A trigger with an empty buffer is also fine; the response comes back once the digest has been queued for delivery, not when it's delivered. After deleting a schedule, matching alerts keep buffering but no longer go out on a timer.

## Errors

| Status | When |
|---|---|
| `400` | Unknown alert type, missing `active_hours`, or `active_hours` that isn't a valid schedule |
| `404` | `GET` of a schedule that doesn't exist |
| `503` | The summary store failed to initialise at startup; check the processor log. Turning off `quest_summary_enabled` does **not** cause this — the endpoints stay live. (Before 5.3 the error message wrongly pointed at `quest_summary_enabled`.) |

The v2 [full tracking snapshot](v2/tracking.md#full-snapshot-get-tracking) also includes the user's summaries.
