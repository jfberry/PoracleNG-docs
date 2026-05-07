# Auto-create API

The auto-create API exposes the channel-template store and the bulk sync runner. All endpoints sit under `/api/autocreate/` and require the `X-Poracle-Secret` header (see [API authentication](index.md#authentication)).

For full request/response shapes, parameter detail, and error codes, see the upstream API reference: [`api.md` in the PoracleNG repository](https://github.com/jfberry/PoracleNG/blob/main/api.md). This page is a capability outline.

## What you can do over HTTP

- **Read, validate, and write** the channel template file (`config/channelTemplate.json`) without shell access to the host.
- **Lint a template** before saving — the same validator the editor uses, accessible standalone.
- **Delete a single named template** without rewriting the whole array.
- **Fetch schema metadata** (channel types, button styles, permission flags) so an editor UI can render typed inputs and dropdowns.
- **Trigger a bulk sync** of one rule or all rules, with the same `dryrun` / `removals` / `reset` / `force` options as the Discord command.

## Endpoints

### Templates

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/autocreate/templates` | Read the live `channelTemplate.json`. |
| `POST` | `/api/autocreate/templates` | Validate and write the full template array. The previous version is snapshotted into `config/backups/`. |
| `POST` | `/api/autocreate/templates/validate` | Lint a template payload without writing. Returns a list of errors and warnings. |
| `DELETE` | `/api/autocreate/templates/:name` | Remove a single template by `name`. |
| `GET` | `/api/autocreate/templates/schema` | Static schema metadata — enums, permission flags, and field descriptions. Intended for editor UIs. |

### Sync

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/api/autocreate/run` | Trigger a bulk sync. Body specifies the rule (omit for "all"), and the `dry_run`, `removals`, `force`, `reset` flags. Returns a per-rule report including `Status`, created / reused / removed / skipped lists, and any errors. |

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" \
  -H "Content-Type: application/json" \
  -d '{"rule": "uk-areas", "dry_run": true}' \
  http://localhost:3030/api/autocreate/run
```

`Status` values: `"ok"` (normal), `"busy"` (another sync of this rule already running), `"safety_blocked"` (removal threshold exceeded — re-run with `force` to override).

## Related

- [Channel Auto-create configuration](../configuration/autocreate.md) — template format, rules, sync semantics, and the equivalent Discord commands.
- [Poracle Config UI](../configuration/config-ui.md) — visual editor that uses these endpoints.
