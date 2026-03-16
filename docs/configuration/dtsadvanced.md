# Advanced DTS

Advanced features for alert templates. For basics, see [Alert Templates (DTS)](dts.md).

## Handlebars Helpers

PoracleNG includes several custom Handlebars helpers for use in templates.

### Conditionals

```handlebars
{{#if iv}}IV: {{iv}}%{{/if}}
{{#unless iv}}No IV data{{/unless}}
{{#Pokemon ifeq "Ditto"}}It's a Ditto!{{/Pokemon ifeq}}
```

### Math

```handlebars
{{Pokemon math id "+" 1}}
{{Pokemon math level "*" 2}}
```

### Formatting

```handlebars
{{Pokemon pad id 3}}           {{!-- Zero-pad to 3 digits: 025 --}}
{{Pokemon floor weight}}       {{!-- Round down --}}
{{Pokemon ceil height}}        {{!-- Round up --}}
{{Pokemon round iv 1}}         {{!-- Round to 1 decimal --}}
```

### GetPokemon Powerup Cost

Calculate stardust and candy costs:

```handlebars
{{Pokemon Pokemon getpokemon_powerup_cost id level 40}}
```

### Conditional Blocks

```handlebars
{{#Pokemon Pokemon compare iv ">=" 90}}
  High IV Pokemon!
{{/Pokemon Pokemon compare}}
```

### Time Helpers

```handlebars
{{Pokemon Pokemon Pokemon formatTime despawn_time "HH:mm:ss"}}
```

## Multiple Templates

Create multiple template styles for different use cases:

```json
{
  "pokemon": {
    "discord": {
      "1": { "embed": { "title": "Compact: {{Pokemon name}} {{iv}}%" } },
      "2": { "embed": { "title": "Detailed: {{Pokemon name}}", "description": "Full stats..." } },
      "3": { "embed": { "title": "PVP Focus: {{Pokemon name}}", "description": "PVP ranks..." } }
    }
  }
}
```

Users select templates with the `template` flag:

```
!track pikachu template2
```

## Custom Emoji

Define custom emoji mappings in `config/emoji.json`:

```json
{
  "pokemon_1": "<:bulbasaur:123456789>",
  "type_fire": "<:fire:123456789>",
  "weather_1": "<:sunny:123456789>"
}
```

Reference in templates with the emoji key.

## Broadcast Templates

Define reusable broadcast message templates in `config/broadcast.json` for admin broadcasts.

## Channel Templates

Auto-create Discord channels from templates in `config/channelTemplate.json`. This is useful for automatically setting up alert channels for new areas.

## Template Testing

Test your templates with the `!test` command:

```
!test pokemon
!test raid
!test quest
```

This sends a test alert using sample data from `config/testdata.json` (or the bundled fallback).

## Tips

- Always test templates after changes with `!test`
- Use partials to avoid duplicating common template sections
- The `examples/dts/` directory contains community-contributed templates for inspiration
- Variables that have no data will render as empty strings — use `{{#if}}` blocks for optional fields
- Discord embeds have character limits: title (256), description (4096), footer (2048)
