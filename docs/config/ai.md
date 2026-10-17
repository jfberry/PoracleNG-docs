# `[ai]`

Natural-language command assistant. It turns requests such as *"mewtwo raids within 2km"* into ordinary Poracle commands and shows them to the user to copy and run.

The assistant is a built-in rule-based parser. It does not call any external AI service, and does not run commands itself; it only suggests them. It understands English input only.

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `enabled` | bool | `false` | Load the parser and enable the `!ask` command. When `false`, `!ask` replies with 🙅. |
| `suggest_on_dm` | bool | `false` | When a user sends a DM that is not a recognised command, reply with a *"Did you mean: …"* suggestion. Requires `enabled = true`. Discord and Telegram. |

```toml
[ai]
enabled = true
suggest_on_dm = true
```

## Usage

```
!ask perfect dragonite
```

The bot replies with one or more suggested commands, e.g.:

```
Did you mean:
`!track dragonite iv100`

Copy and paste to run.
```

Changing either key requires a restart.
