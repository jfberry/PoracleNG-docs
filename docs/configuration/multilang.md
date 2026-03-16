# Multi-Language

PoracleNG supports multiple languages for pokemon names, move names, and other game data. Users can switch between available languages.

## Default Language

Set the default locale for all users:

```toml
[general]
locale = "en"                            # Default locale (en, fr, de, it, ru, etc.)
```

## Secondary Language

A secondary language can be configured for alternative translations in DTS templates (useful for providing English names for web links alongside localized names):

```toml
[locale]
language = "en"                          # Secondary language for {{alt}} variables
```

## Available Languages

Configure which languages users can switch between with the `!language` command. Each language can have its own command keywords:

```toml
[general.available_languages.en]
poracle = "poracle"
help = "help"

[general.available_languages.de]
poracle = "dasporacle"
help = "hilfe"

[general.available_languages.fr]
poracle = "poracle"
help = "aide"
```

When configured, users can switch with:

=== "Discord"

    ```
    !language de
    ```

=== "Telegram"

    ```
    /language de
    ```

## Custom Translations

Override or add translations by placing a `custom.<lang>.json` file in `config/`:

```
config/custom.en.json
config/custom.de.json
```

These are merged with the built-in translations from `resources/`.

## Locale Formatting

Control date and time display format:

```toml
[locale]
timeformat = "en-gb"                     # Locale for date/time formatting
time = "LTS"                             # Time format
date = "L"                               # Date format
address_format = "{{{streetName}}} {{streetNumber}}"
```
