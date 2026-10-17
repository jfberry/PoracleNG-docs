# DTS Field Reference

This page lists the fields each alert template type can use. For how templates are loaded, chosen and formatted, see [Alert Templates (DTS)](dts.md). For helpers, custom maps and emoji, see [Advanced DTS](dtsadvanced.md).

!!! note "Versions"
    `main` is PoracleNG 5.2.x and `develop` is 5.3.0-dev. Fields marked **5.3+** exist only on `develop`. Everything else works on both.

!!! tip "See exactly what a template gets"
    The DTS editor endpoint `POST /api/dts/enrich` (used by PoracleWeb and the [Config UI](config-ui.md)) returns the complete variable map for a sample webhook. `GET /api/dts/fields` and `GET /api/dts/fields/{type}` return the editor's field catalogue. The catalogue is maintained by hand, so where it disagrees with this page, check the enrich output. On 5.3+ it only lists fields the code produces, under their real names; the 5.2 catalogue offers some that don't exist (for example `isNight`, `areasList`, `weatherTthh`, `totalStationedPokemon`, `cp20`, `sunriseTime`), and `distance`/`bearing` for every type.

## How Fields Are Resolved

A template variable is looked up in these layers, and the first layer that has it wins:

1. **Per-user**: PVP display, distance, bearing (pokemon only), plus `userDistanceTrack`
2. **Emoji**: emoji keys already resolved for the recipient's platform (`genderEmoji`, `typeEmoji` and so on)
3. **Per-language**: names translated into the recipient's language, plus the address in that language
4. **Computed**: `id`, `time`, `tthd`/`tthh`/`tthm`/`tths`, `now`, `nowISO`, `areas`, `matched`, `genderData`, `megaName`, `weatherChange`, and sanitised user text (see [Escaping](dts.md#escaping))
5. **Base enrichment**: everything the processor works out once per event
6. **Aliases**: old or alternative names for fields, listed for each type below
7. **Raw webhook**: every field in the scanner's webhook (usually Golbat), with its original snake_case name
8. **`[general] dts_dictionary`**: your own key/value pairs from `config.toml`

Raw webhook fields are always available. For example, a pokemon template can use `{{display_pokemon_id}}` or `{{capture_1}}` even though Poracle doesn't enrich them. Use the enriched camelCase fields where they exist: they're translated, formatted and don't change when the scanner does.

```toml
[general.dts_dictionary]
discordInvite = "https://discord.gg/abc"
```

```handlebars
Join us: {{discordInvite}}
```

## Common Fields

### Location and Address

These are available on every alert type that comes from a webhook. The address fields need a [geocoding provider](geocoding.md). If a user's language differs from `[general] locale`, the address is looked up again in their language.

| Field | Description |
|---|---|
| `latitude`, `longitude` | Alert location. For nests it's the nest centre, and for `fort-update` it's the fort (**5.3+**). |
| `addr` | Address built from `[locale] address_format` |
| `formattedAddress` | The provider's full formatted address |
| `streetName`, `streetNumber` | Street name and number |
| `neighbourhood`, `suburb`, `city`, `county`, `state`, `zipcode` | Address parts |
| `country`, `countryCode`, `flag` | Country name, two-letter code and flag emoji |
| `intersection` | Nearest street intersection (`Street1 & Street2`) from GeoNames. Empty when the lookup is turned off or nothing is nearby. |
| `areas` | Comma-separated names of the matched geofence areas (only areas with `displayInMatches`) |
| `matched` | The same area names, lowercased, as an array: `{{#each matched}}{{map 'arealist' this}}{{/each}}` |

### Map Links and Images

| Field | Description |
|---|---|
| `googleMapUrl`, `appleMapUrl`, `wazeMapUrl` | Navigation links |
| `rdmUrl`, `reactMapUrl`, `diademUrl`, `rocketMadUrl` | Scanner map links. Each is set only when its `[general]` URL is configured. |
| `staticMap` | Static map image URL (see [Static Maps](staticmaps.md)) |
| `imgUrl`, `imgUrlAlt`, `stickerUrl` | Icon URLs from the primary uicons repo, the alternative repo and the sticker repo |
| `mapurl`, `applemap`, `staticmap` | *Deprecated* aliases for `googleMapUrl`, `appleMapUrl` and `staticMap` |

`weatherchange` alerts have no map links.

### Time

| Field | Description |
|---|---|
| `time` | Formatted time in the alert's local timezone. Eggs use the **hatch** time. Every other type uses `disappearTime`. |
| `disappearTime` | Formatted end time: despawn, raid end, lure end, invasion end, quest reset (midnight) or nest rotation |
| `distime` | *Deprecated* alias for `disappearTime` |
| `tthd`, `tthh`, `tthm`, `tths` | Days, hours, minutes and seconds left until `time` |
| `now` | Current time, RFC 3339, UTC |
| `nowISO` | Current time, ISO 8601 with milliseconds, UTC |
| `nightTime`, `dawnTime`, `duskTime` | Booleans for the time of day at the alert location. Not set for `quest` or `nest`. |

Times are formatted using `[locale] time`. These types have no `time`/`tth*` fields: `gym` (it uses `conqueredTime` and a fixed 1-hour TTH), `fort-update`, `weatherchange` (use `weatherTth`), `questSummary`, `greeting` and `help`.

#### Unix timestamps (for Discord `<t:N:R>`)

| Type | Field |
|---|---|
| `monster`, `monsterNoIv`, `monsterChanged` | `despawnTimestamp` (also `disappear_time`) |
| `raid`, `egg`, `rsvpChanges` | `endTimestamp`, `hatchTimestamp`, `start`, `end` |
| `invasion`, `incident`, `showcase` | `expirationTimestamp` |
| `lure` | `expirationTimestamp` |
| `maxbattle` | `endTimestamp` (also `battle_end`), `battle_start` |
| `quest` | `disappear_time` (end of day) |

### Distance and Bearing

| Field | Types | Description |
|---|---|---|
| `distance` | pokemon types | Metres from the user's location to the alert |
| `bearing` | pokemon types | Bearing from the user, in degrees |
| `bearingEmoji` | pokemon types | Arrow emoji for the direction |
| `userTrackDistance` | pokemon types | The matched rule's distance in metres (`0` when the rule is area-based). If several rules match, it's the largest. |
| `userDistanceTrack` | all alert types | `true` when the matched rule was distance-based rather than area-based |

!!! note
    The "pokemon types" are `monster`, `monsterNoIv` and `monsterChanged`. Other types work out the distance internally, but only pokemon alerts expose it to templates.

### Game Weather

Available on `monster`/`monsterNoIv`/`monsterChanged`, `raid`/`egg`/`rsvpChanges`, `invasion`/`incident`/`showcase` and `maxbattle`.

| Field | Description |
|---|---|
| `gameWeatherId` | Current in-game weather in the alert's S2 cell |
| `gameWeatherName`, `gameWeatherNameEng` | Translated and English weather names |
| `gameWeatherEmoji` | Weather emoji |
| `gameweather`, `gameweatheremoji` | *Deprecated* aliases |

### Weather Boost

Available on pokemon types, `raid`/`rsvpChanges` and `maxbattle`.

| Field | Description |
|---|---|
| `boosted` | `true` if the current weather boosts this pokemon |
| `boostWeatherId` | Boosting weather id, or `""` if not boosted |
| `boostWeatherName`, `boostWeatherNameEng` | Boosting weather name, translated and English |
| `boostWeatherEmoji` | Boosting weather emoji (empty if not boosted) |
| `boostingWeatherIds` | Every weather that boosts this pokemon's types |
| `boostingWeathersEmoji` | Those weathers' emoji joined together. Raid and maxbattle only: pokemon alerts don't set it, although the upstream DTS.md and the editor's field list show it for pokemon. |
| `boost`, `boostemoji` | *Deprecated* aliases |

## Pokemon Names

Every type that names a pokemon uses the same fields:

| Field | Description |
|---|---|
| `name`, `nameEng` | Species name, translated and English |
| `formName`, `formNameEng` | Form name exactly as translated (may be "Normal") |
| `formNormalised`, `formNormalisedEng` | Form name, empty when the form is Normal |
| `fullName`, `fullNameEng` | Name plus form, e.g. `Pikachu Libre`. Megas use the localised mega name, e.g. `Mega Charizard X`. If the spawned pokemon, raid boss or max battle boss wears a costume, it's appended in brackets: `Pikachu (Holiday 2016)`. |
| `costumeName` | Translated costume name. Empty when there's no costume. Set on pokemon, raid and maxbattle. |

!!! note
    The `{{#pokemon}}` block helper builds names differently: it puts the form in brackets, e.g. `Pikachu (Libre)`. See [Advanced DTS](dtsadvanced.md#pokemon-block-helper).

## Pokemon (`monster` / `monsterNoIv`)

`monster` is used for encountered pokemon (with IVs). `monsterNoIv` is used for pokemon that haven't been encountered. If no `monsterNoIv` template exists, `monster` is used instead.

### Identity

| Field | Alias / raw | Description |
|---|---|---|
| `pokemon_id`, `pokemonId`, `id` | | Pokedex number |
| `formId` (**5.3+**) | `form` | Form id |
| `encounterId` | `encounter_id` | Encounter id |
| `spawnpointId`, `pokestopId` | `spawnpoint_id`, `pokestop_id` | Spawn point and nearby pokestop ids |
| `pokestopName` | `pokestop_name` | Nearby pokestop name, if the scanner sends it |
| names | | See [Pokemon Names](#pokemon-names). Also `formname` (*deprecated*). |
| `generation`, `generationRoman` | | Generation number and Roman numeral |
| `generationName`, `generationNameEng` | | e.g. `Kanto` |
| `legendary`, `mythic`, `ultraBeast` | | Booleans |
| `rarityGroup`, `rarityName`, `rarityNameEng` | | Rarity group (1–6) and its name |
| `disguisePokemonName`, `disguiseFormName` | | What a Ditto is disguised as (`disguisePokemonId`/`disguiseFormId` are the ids) |

### Stats

Pokemon that haven't been encountered have `iv = -1`, and `cp`, `level`, `atk`/`def`/`sta` and the catch rates are all `0`.

| Field | Alias | Description |
|---|---|---|
| `encountered` | | `true` when IVs are known |
| `iv` | | IV percentage as a string with 2 decimals; use `{{round iv}}` |
| `atk`, `def`, `sta` | `individual_attack/defense/stamina` | IVs |
| `cp`, `level` | | CP and level |
| `ivColor` | `ivcolor` | Colour for the IV range, from `[discord] iv_colors` |
| `weight`, `height` | | Strings with 2 decimals |
| `size`, `sizeName`, `sizeNameEng` | | Size (1 = XXS … 5 = XXL) |
| `catchBase`, `catchGreat`, `catchUltra` | | Catch chance as a percentage |
| `baseStats` | | `{baseAttack, baseDefense, baseStamina}` |
| `confirmedTime`, `verified` | | Whether the despawn time is verified |
| `tthSeconds` | | Total seconds until despawn |
| `seenType` | | See below |
| `cell_coords` | | S2 cell corners, for pokemon seen in a cell |

`seenType` values:

| `seenType` | Golbat `seen_type` | Meaning |
|---|---|---|
| `encounter` | `encounter` | Fully encountered: IVs, moves and PVP are known |
| `wild` | `wild` | Seen in the wild, without IVs |
| `pokestop` | `nearby_stop` | Seen near a fort (imprecise location) |
| `cell` | `nearby_cell` | Seen in an S2 cell (imprecise location). Also used when an RDM-style scanner sends no spawn point or stop. |
| `lure` | `lure_wild`, `lure` | Lure spawn, not yet encountered |
| `lure_encounter` | `lure_encounter` | Lure spawn, encountered |
| `tappable` | `tappable_encounter`, `tappable_lure_encounter` | Encountered tappable (including lured tappables) |
| `""` | anything else | Unknown source |

### Types, Weaknesses and Moves

| Field | Alias | Description |
|---|---|---|
| `typeName` | | Translated type names, comma-separated |
| `typeNameEng` | | English type names (array) |
| `types` | | Type ids (array) |
| `typeEmoji`, `emojiString` | | Type emoji joined together |
| `emoji` | | Type emoji (array) |
| `color` | | Colour of the primary type |
| `weaknessList` | | Categories `{value, typeName, typeEmoji, types: [{typeId, name, emoji}]}` |
| `weaknessEmoji` | | Flat summary, e.g. `2x💧⚡ 4x🪨 ` |
| `quickMoveId`, `chargeMoveId` | | Move ids (encountered pokemon only) |
| `quickMoveName`, `chargeMoveName` | `quickMove`, `chargeMove` | Translated move names |
| `quickMoveNameEng`, `chargeMoveNameEng` | | English move names |
| `quickMoveEmoji`, `chargeMoveEmoji` | `move1emoji`, `move2emoji` | Emoji for the move's type |

### Weather Forecast

The fields below are filled only when the forecast for the next hour would change this pokemon's boost before it despawns.

| Field | Description |
|---|---|
| `weatherChange` | Ready-made line, e.g. `⚠️ Possible weather change at 14:00 : Sunny ☀ ➡️ Rain 🌧` |
| `weatherChangeTime` | Time of the change (without seconds) |
| `weatherCurrent`, `weatherNext` | Weather ids |
| `weatherCurrentName`, `weatherCurrentEmoji` | Current forecast weather (`unknown`/❓ when the pokemon would lose its boost) |
| `weatherNextName`, `weatherNextEmoji` | Next weather |
| `weatherForecastCurrent`, `weatherForecastNext`, `nextHourTimestamp` | Raw forecast data |

### Evolutions

| Field | Description |
|---|---|
| `hasEvolutions` | The species evolves |
| `hasMegaEvolutions` | **5.3+:** a mega evolution exists anywhere in the evolution chain (Electrike → Mega Manectric). On 5.2 it only checks the species itself. |
| `evolutions` | Every later evolution, found by walking the chain (Bulbasaur lists Ivysaur and Venusaur) |
| `megaEvolutions` | Mega and primal forms available along the chain |
| `prevEvolutions` | Pokemon that evolve into this one: `{id, form, name, formName, formNormalised, fullName, evolutionRequirement}` |

Each `evolutions` entry has: `id`, `form`, the [name fields](#pokemon-names) (`name`, `formName`, `formNormalised`, `fullName`, and on **5.3+** also `nameEng`, `formNameEng`, `formNormalisedEng`, `fullNameEng`), `typeName`, `typeEmojiKeys`, `baseStats` and `evolutionRequirement`. **5.3+** adds `typeEmoji` (joined string) and `emoji` (array).

Each `megaEvolutions` entry has: `evolution` (temp-evolution id), `fullName`, `typeName`, `typeEmojiKeys` and `baseStats`. **5.3+** adds `fullNameEng`, `typeEmoji` and `emoji`.

Inside a loop, use `../` to reach the alert's own fields:

```handlebars
{{#each evolutions}}{{fullName}}: {{calculateCp baseStats ../level ../atk ../def ../sta}} CP
{{/each}}
```

### Gender, Shiny and Events

| Field | Description |
|---|---|
| `genderData` | `{name, emoji}` in the user's language |
| `genderDataEng` | `{name, emoji}` in English |
| `genderEmoji`, `genderNameEng` | Gender emoji and English name |
| `shinyPossible`, `shinyPossibleEmoji`, `shinyStats` | Shiny is possible, the ✨ emoji, and the odds (1 in N). Set when shiny statistics are available. |
| `futureEvent` | `true` if an event starts or ends before despawn |
| `futureEventName`, `futureEventTime` | Event name and start/end time |
| `futureEventTrigger` | `start` or `end` |

### PVP

These fields are set for every recipient:

| Field | Description |
|---|---|
| `bestGreatLeagueRank`, `bestUltraLeagueRank`, `bestLittleLeagueRank` | Best rank in each league, as an **integer**. Mega ranks are excluded. `4096` means no base-form rank. Not set when the webhook has no PVP data for that league. |
| `bestGreatLeagueRankCP`, `bestUltraLeagueRankCP`, `bestLittleLeagueRankCP` | CP at that rank |
| `pvp_rankings_great_league` (and `_ultra_`, `_little_`) | Every enriched rank entry, without per-user filtering |
| `pvpBestRank`, `pvpEvolutionData` | Raw per-league data |

These fields depend on who receives the alert (their PVP rules and `[pvp]` display settings):

| Field | Description |
|---|---|
| `pvpGreat`, `pvpUltra`, `pvpLittle` | Ranks to display in each league. Only ranks within `[pvp] display_max_rank` and the league's minimum CP are included. When `filter_by_track` is on, only ranks matching the user's PVP rules are kept. |
| `pvpGreatBest`, `pvpUltraBest`, `pvpLittleBest` | `{rank, name, list}`: the best rank, the names of the pokemon at that rank (comma-separated), and their entries |
| `pvpAvailable` | `true` if any league has a rank to display |
| `userHasPvpTracks` | `true` if one of the matched rules was a PVP rule |
| `pvpUserRanking` | The matched rule's worst rank (`0` if it wasn't a PVP rule) |
| `pvpDisplayMaxRank`, `pvpDisplayGreatMinCP`, `pvpDisplayUltraMinCP`, `pvpDisplayLittleMinCP` | The `[pvp]` display settings |

Each PVP rank entry has:

| Field | Description |
|---|---|
| `rank`, `cp`, `level`, `cap`, `capped` | Rank data. `cap` is the level cap the rank was calculated for. |
| `levelWithCap` | Level, or `level/cap` when the rank isn't capped |
| `percentage` | Stat product percentage (0–100, 2 decimals) |
| `pokemon`, `pokemonId`, `form` | The pokemon this rank is for (it may be an evolution) |
| `evolution` | `0` for the normal form. Otherwise the mega id: `1` Mega, `2` Mega X, `3` Mega Y. |
| `name`, `fullName`, `formName`, `formNormalised`, `nameEng`, `fullNameEng`, `formNormalisedEng` | Names. For megas, `fullName` is the mega name. |
| `baseStats`, `baseAttack`, `baseDefense`, `baseStamina` | Base stats |
| `matchesUserTrack`, `passesFilter` | Whether the rank matches one of the user's PVP rules |

```handlebars
{{#if pvpGreat}}**Great league:**
{{#each pvpGreat}} - {{fullName}} #{{rank}} @{{cp}}CP (Lvl. {{levelWithCap}})
{{/each}}{{/if}}
```

## Pokemon Changed (`monsterChanged`)

This template is sent as a reply to an earlier pokemon alert when the same encounter changes after it was encountered: a different species or form (`species`), or different stats after a weather-boost change or a re-scan (`stats`). It uses the **same template id** the user's original alert used. You can turn it off with `[tracking] pokemon_change_tracking = false`.

All [pokemon fields](#pokemon-monster-monsternoiv) are available and describe the **new** sighting. These fields are added:

| Field | Description |
|---|---|
| `changeType` | `species` or `stats` |
| `changeTypeText` | Translated label for the change |
| `original` | The earlier sighting, e.g. `{{original.fullName}}`, `{{original.cp}}` |

`original` holds the enrichment for the earlier sighting in the recipient's language: names, IVs/CP/level, moves, `gameWeatherName` and so on. It does **not** include PVP, `staticMap`, emoji (only their `*EmojiKey` values), computed fields (`id`, `time`, `tth*`), aliases (`mapurl`, `formId` …) or raw webhook fields. If that enrichment isn't available, a smaller set is used: `pokemonId`, `formId`, `gender`, `weatherId`, `weatherName`, `cp`, `atk`, `def`, `sta`, `iv`, `encountered`, `name`, `formName` and `fullName` (`Name (Form)`).

!!! tip
    A species change can arrive before the new pokemon is encountered. Wrap the stats in `{{#if encountered}}…{{/if}}`, as the shipped template does.

## Raid (`raid`), Egg (`egg`) and RSVP Updates (`rsvpChanges`)

### Gym and level (raid, egg and rsvpChanges)

| Field | Alias / raw | Description |
|---|---|---|
| `gymName`, `gymUrl`, `gymId` | `gym_name`, `gym_url`, `gym_id` | Gym name (sanitised), image and id |
| `teamId` | `team_id` | 0 uncontested, 1 Mystic, 2 Valor, 3 Instinct |
| `teamName`, `teamNameEng`, `teamEmoji`, `teamColor` | | Team name, English name, emoji and colour |
| `gymColor`, `color` | | Team colour (`color` is deprecated) |
| `ex` | `ex_raid_eligible`, `is_ex_raid_eligible` | EX eligible (see note) |
| `level` | | Raid level |
| `levelName`, `levelNameEng` | | e.g. `Level 5`, `Mega` |
| `start`, `end`, `hatchTimestamp`, `endTimestamp` | | Unix times |
| `hatchTime`, `hatchtime` | | Formatted hatch time (eggs) |
| `firstNotification` | | `true` the first time this raid is alerted |
| `campfireUrl` | | Campfire app deep link |
| `rsvps` | | Upcoming RSVP slots: `{timeSlot, time, goingCount, maybeCount}` (also `timeslot`, `going_count`, `maybe_count`) |

!!! note
    On 5.3+ `{{ex}}` is true when either `ex_raid_eligible` (Golbat) or the older `is_ex_raid_eligible` is set. On 5.2 it only reads `is_ex_raid_eligible`, so with Golbat it's always empty; use `{{ex_raid_eligible}}` there.

For an **egg**, `time` is the **hatch time**, `tth*` counts down to the hatch, and `disappearTime` is the raid end.

### Boss (raid and hatched rsvpChanges)

| Field | Alias / raw | Description |
|---|---|---|
| `pokemon_id`, `pokemonId`, `id` | | Boss pokedex number |
| `formId` (**5.3+**) | `form` | Form id |
| names | `formname` | See [Pokemon Names](#pokemon-names), including `costumeName` |
| `megaName` | | Same as `fullName` (including mega name, form and costume) |
| `evolution`, `evolutionName` | | Temp-evolution id and its name (`Mega`, `Primal` …) |
| `cp`, `gender`, `costume`, `alignment` | | Raw webhook values |
| `quickMoveName`, `chargeMoveName` (+`Eng`, `Id`, `Emoji`) | `move1`, `move2`, `quickMove`, `chargeMove`, `move1emoji`, `move2emoji` | Moves |
| `typeName`, `typeNameEng`, `typeEmoji`, `emoji`, `types` | | Types |
| `weaknessList`, `weaknessEmoji`, `baseStats` | | As for pokemon |
| `generation`, `generationRoman`, `generationName(Eng)` | | Generation |
| `genderData`, `genderEmoji`, `genderNameEng` | | Gender |
| `shinyPossible`, `shinyPossibleEmoji` | | Shiny |
| `hasEvolutions`, `hasMegaEvolutions`, `evolutions`, `megaEvolutions`, `prevEvolutions` | | See [Evolutions](#evolutions) |

Raids have no forecast fields: `weatherChange`, `weatherCurrentName`, `weatherNextName` and `weatherChangeTime` are never set, even though the upstream DTS.md lists them for raids. Only `weatherForecastCurrent`, `weatherForecastNext` and `nextHourTimestamp` are available.

### `rsvpChanges`

When a raid's RSVP counts change, users who already received the raid or egg alert get this short follow-up, sent as a reply. It is only used if an `rsvpChanges` template exists with exactly the user's template id. Otherwise the full `raid`/`egg` template is sent again. Users with the `edit` option get the full template edited in place instead. All raid (or egg) fields are available. For an egg, the boss fields are empty, so you can branch on `{{#if pokemonId}}`. There's an example in `examples/dts/rsvpChanges/`.

## Quest (`quest`)

| Field | Alias / raw | Description |
|---|---|---|
| `pokestopName`, `pokestopUrl`, `pokestopId` | `pokestop_name`, `pokestop_url`, `pokestop_id`, `name`, `url` | Pokestop (the name is sanitised) |
| `questString`, `questStringEng` | `quest_task` | Translated task |
| `rewardString`, `rewardStringEng` | `quest_reward` | Every reward as text |
| `conditionString`, `conditionStringEng` | `quest_conditions` | Task conditions, e.g. `Excellent Throw, Curve Ball` |
| `conditionList`, `conditionListEng` | | `{type, name, formatted}` for each condition |
| `with_ar` | raw | `true` for the AR quest |
| `monsters` (also `monsterList`) | | `{pokemonId, formId, shiny, name, formName, fullName, nameEng, fullNameEng}` |
| `monsterNames`, `monsterNamesEng` | | Pokemon rewards, comma-separated |
| `items`, `itemNames`, `itemNamesEng`, `itemAmount` | | `{id, amount, name, nameEng}`. `itemNames` includes amounts above 1. |
| `dustAmount`, `dustText`, `dustTextEng` | | e.g. `500 Stardust` |
| `pokecoinAmount`, `pokecoinText`, `pokecoinTextEng` | | e.g. `10 PokéCoins` |
| `energyMonsters`, `energyMonstersNames(Eng)`, `energyAmount` | | Mega energy `{pokemonId, amount, name, nameEng}` |
| `candy`, `candyMonstersNames(Eng)`, `candyAmount` | | Candy `{pokemonId, amount, name, nameEng}` |
| `isShiny`, `shinyPossible`, `shinyPossibleEmoji`, `shinyStats` | | Shiny data for a pokemon reward |
| `baseStats` | | Base stats of the reward pokemon (for `calculateCp`) |
| `futureEvent`, `futureEventName`, `futureEventTime`, `futureEventTrigger` | | Event that starts or ends before the quest resets |
| `time`, `disappearTime`, `tth*` | | End of the local day |

## Quest Summary (`questSummary`)

Users who track quests with `summary` get their buffered quests as grouped messages at scheduled times: one message per reward, split into several messages if the group is too big. See [Quests](../userguide/quests.md).

| Field | Description |
|---|---|
| `rewardType` | 2 item, 3 stardust, 4 candy, 7 pokemon, 8 PokéCoins, 12 mega energy |
| `reward`, `rewardForm` | Reward id (item id, dust or coin amount, or pokemon id) and pokemon form |
| `rewardName` | Translated reward name, e.g. `Razz Berry`, `1500 Stardust`, `Lapras Candy` |
| `imgUrl`, `stickerUrl` | Reward icon and sticker |
| `staticMap` | Map with a pin for each pokestop in **this** message |
| `count` | Pokestops in the whole group (across all messages) |
| `chunk`, `chunks` | This message's number and the total, e.g. `{{#if (gt chunks 1)}}({{chunk}}/{{chunks}}){{/if}}` |
| `quests` | One entry per pokestop. Each has every [`quest`](#quest-quest) field plus `pokestopName`, `pokestopUrl`, `latitude`, `longitude`, `pokestop_id` and `withAR`. |
| `areas`, `matched`, `now` | Computed fields |

Pokestop names are sanitised (**5.3+**). There are no top-level address, map-link or time fields; use the ones inside `{{#each quests}}`.

## Invasion (`invasion`)

| Field | Alias / raw | Description |
|---|---|---|
| `pokestopName`, `pokestopUrl`, `pokestopId` | `name`, `url`, `pokestop_*` | Pokestop |
| `gruntTypeId`, `displayTypeId` | | Grunt and display type ids |
| `gruntName` | | Translated grunt name, e.g. `Female Grunt` |
| `gruntType` | | English type, e.g. `Fire` (good for `{{#eq gruntType 'Fire'}}`) |
| `gruntTypeName` | | Translated type |
| `gruntTypeColor`, `gruntTypeEmoji` | | Type colour and emoji |
| `gruntGender` | `gender` | 1 male, 2 female |
| `genderData`, `genderEmoji`, `genderNameEng` | | Gender |
| `gruntRewards` | | Possible rewards as text (`85%: …\n15%: …`) |
| `gruntRewardsList` | | `{first: {chance, monsters}, second: {chance, monsters}}`. Each monster has `{id, formId, name, formName, fullName}`. |
| `gruntRewardIDs` | | `[{pokemon_id, form}]` for the first reward slot |
| `gruntLineupList` | | When the scanner sends the confirmed lineup: `{confirmed: true, monsters: [{id, formId, name, formName, fullName}]}` |
| `expiration`, `incidentExpiration`, `expirationTimestamp` | | Unix end time |

## Incident (`incident`)

Gold-Stops (display type 7) and Kecleon (8) use this template. Showcases (9) use [`showcase`](#showcase-showcase). It has the pokestop, location, time and weather fields that invasions have, without the grunt and reward fields, plus:

| Field | Description |
|---|---|
| `incidentTypeName` | Translated event name (alias for `gruntName`) |
| `displayType` | `7`, `8` or `9` (alias for `displayTypeId`) |
| `incidentEmoji` | Event emoji (alias for `gruntTypeEmoji`) |
| `color` | Event colour (alias for `gruntTypeColor`) |
| `gruntType` | Lowercase event slug: `gold-stop`, `kecleon`, `showcase` |

## Showcase (`showcase`)

Used for Pokémon Showcase contests. They're tracked as incidents (the `showcase` incident type) but rendered with this template. Every leaderboard change re-fires the alert, and users with the `edit` option get their existing message updated in place. It has the pokestop, location, time and weather fields (including `displayType` and `expirationTimestamp`), but not `incidentTypeName`, `incidentEmoji` or `color`.

| Field | Description |
|---|---|
| `showcasePresent` | `true` when leaderboard data is present |
| `showcaseTotalEntries` | Number of entries |
| `showcaseLastUpdate`, `showcaseLastUpdateFormatted` | Last leaderboard update, as a unix time and formatted (UTC) |
| `showcase` | Top entries (up to 3) |
| `showcaseFirst` | The leading entry |
| `showcaseFocusPresent` | `true` when the contest's focus is known |
| `showcaseFocusType` | `pokemon`, `type`, `alignment`, `class`, `family`, `buddy`, `generation`, `hatched`, `mega` or `shiny` |
| `showcaseFocusCategory` | Translated category, e.g. `Type` |
| `showcaseFocusName` | Translated value, e.g. `Steel` or `3+`. Empty for `hatched`/`mega`/`shiny`. |
| `showcaseFocusEmoji` | Emoji for the focus category (**5.3+**; always empty on 5.2). Defaults to 🏆; override per focus with the `showcase-focus-<type>` keys in `emoji.json` (e.g. `showcase-focus-type`). |

Each `showcase` entry has: `rank`, `score`, `scoreFormatted`, `pokemonId`, `pokemonName`, `formId`, `formName`, `fullName` (with a Shadow/Purified prefix and mega name), `costumeId`, `costumeName`, `genderId`, `genderName`, `genderEmojiKey`, `shiny`, `shinyEmoji`, `tempEvolutionId`, `tempEvolutionName`, `alignment`, `alignmentName`, `badge`, `background` and `imgUrl`.

```handlebars
{{#if showcaseFocusPresent}}Featuring {{showcaseFocusCategory}}{{#if showcaseFocusName}}: {{showcaseFocusName}}{{/if}}{{/if}}
{{#if showcasePresent}}{{#each showcase}}{{rank}}. {{fullName}}{{#if shiny}} ✨{{/if}} — {{scoreFormatted}}
{{/each}}{{/if}}
```

## Lure (`lure`)

| Field | Alias / raw | Description |
|---|---|---|
| `pokestopName`, `pokestopUrl`, `pokestopId` | `name`, `url` | Pokestop |
| `lureTypeId` | `lure_id` | 501–506 |
| `lureTypeName`, `lureTypeNameEng` | `lureType` | Lure name |
| `lureTypeColor` | `lureColor` | Colour |
| `lureTypeEmoji` | | Emoji |
| `lure_expiration`, `expirationTimestamp` | | Unix end time |
| `gruntTypeId`, `displayTypeId`, `gruntName`, `gruntTypeName` | | Set when the same pokestop also has an invasion |

## Gym (`gym`)

| Field | Alias / raw | Description |
|---|---|---|
| `gymId`, `gymName`, `gymUrl` | `gym_id`/`id`, `name`, `url` | Gym (the name is sanitised) |
| `teamId`, `teamName`, `teamNameEng`, `teamEmoji`, `teamColor` | `team_id` | New team |
| `oldTeamId`, `oldTeamName`, `oldTeamNameEng` | | Previous team |
| `oldTeamEmoji` | | Emoji of the previous team (`oldTeamId`). **5.3+**; always empty on 5.2. |
| `previousControlId`, `previousControlName`, `previousControlNameEng` | | The last team that held the gym: the last non-zero team Poracle saw on it, so it survives a period of being uncontested. `-1` when unknown. |
| `previousControlTeamEmoji` | | Emoji of the previous controlling team (`previousControlId`). On 5.2 this wrongly showed the `oldTeamId` emoji. |
| `gymColor`, `color` | | New team colour |
| `slotsAvailable`, `oldSlotsAvailable`, `trainerCount`, `oldTrainerCount` | | Defender slots |
| `inBattle`, `ex` | | Booleans |
| `conqueredTime` | | Formatted time of the change |

## Nest (`nest`)

| Field | Alias / raw | Description |
|---|---|---|
| `nestName`, `nestId` | `nest_name`, `nest_id` | Nest (park) name and id |
| `pokemonId` | `pokemon_id` | Pokedex number. `{{id}}` is **not** set for nests. |
| `formId` (**5.3+**) | `form` | Form id |
| names | | See [Pokemon Names](#pokemon-names) (no costume) |
| `typeName`, `typeNameEng`, `typeEmoji`, `emoji`, `types`, `color` | | Types |
| `pokemonCount`, `pokemonSpawnAvg` | `pokemon_avg`, `pokemon_ratio` | Spawn count and average per hour |
| `resetDate`, `resetTime` | | When the nest changed |
| `disappearDate`, `disappearTime`, `time`, `tth*` | | Reset time + 7 days |
| `shinyPossible`, `shinyPossibleEmoji` | | Shiny |
| `zoom`, `map_latitude`, `map_longitude` | | Map position calculated from the nest polygon |

## Max Battle (`maxbattle`)

| Field | Alias / raw | Description |
|---|---|---|
| `stationName`, `stationId` | `station_name`, `station_id` | Power Spot. The name is sanitised on **5.3+**. |
| `pokemonId` | `battle_pokemon_id` | Boss pokedex number |
| `id` | | Boss pokedex number, same as `pokemonId` (**5.3+**). On 5.2 `{{id}}` is the raw station id; use `pokemonId` for the boss and `stationId` for the station. |
| `form`, `formId`, `gender`, `costume`, `alignment` | | Boss identity |
| names, `megaName` | `formname` | See [Pokemon Names](#pokemon-names). `megaName` equals `fullName`. |
| `level`, `levelName`, `levelNameEng` | `battle_level` | Battle level |
| `bread`, `bread_mode` | | 1 Dynamax, 2 Gigantamax |
| `gmax` | | `1` for Gigantamax, `0` otherwise. It's a number, not a boolean. |
| `battle_start`, `battle_end`, `endTimestamp` | | Unix times |
| `total_stationed_pokemon`, `total_stationed_gmax` | | Defenders at the station |
| `quickMoveName`, `chargeMoveName` (+`Eng`, `Id`, `Emoji`) | `quickMove`, `chargeMove`, `move1emoji`, `move2emoji` | Moves |
| `typeName`, `typeNameEng`, `typeEmoji`, `emoji`, `weaknessList`, `weaknessEmoji`, `baseStats` | | Types and stats |
| `generation`, `generationRoman`, `generationName(Eng)` | | Generation |
| `genderData`, `genderEmoji`, `shinyPossible`, `shinyPossibleEmoji` | | Gender and shiny |
| `color` | | Always `D000C0` |

Max battles have no evolution fields. The `gymName`/`gymUrl`/`gymId` aliases exist but are always empty.

## Weather Change (`weatherchange`)

| Field | Alias / raw | Description |
|---|---|---|
| `weatherName`, `weatherEmoji` | `weather`, `weathername`, `weatheremoji` | New weather |
| `oldWeatherName`, `oldWeatherEmoji` | `oldweather`, `oldweathername`, `oldweatheremoji` | Previous weather |
| `gameplay_condition`, `old_gameplay_condition` | raw | Weather ids |
| `s2_cell_id`, `source`, `updated`, `coords` | raw | Weather cell, source (`webhook` or inferred from pokemon), update time and cell corners |
| `weatherTth` | | Time until the next hour: `{{weatherTth.hours}}`, `{{weatherTth.minutes}}`, `{{weatherTth.seconds}}` |
| `enrichedActivePokemons` | | The recipient's tracked pokemon that this change affects (always set) |
| `activePokemons` | | Same list, but only when `[weather] show_altered_pokemon_static_map = true` |

Each affected pokemon has: `pokemon_id`, `form`, `iv`, `cp`, `latitude`, `longitude`, `disappearTime` (a **unix timestamp**), `name`, `nameEng`, `formName`, `formNormalised`, `fullName`, `fullNameEng` and `imgUrl`.

Weather change alerts include the address, `imgUrl`, `staticMap` and time-of-day fields, but no map links, `time` or `tth*`.

## Fort Update (`fort-update`)

| Field | Description |
|---|---|
| `id`, `fortType`, `fortTypeText` | Fort id, `pokestop`/`gym`, and `Pokestop`/`Gym` |
| `changeTypeText` | `New`, `Edit` or `Removal` (in English) |
| `isNew`, `isEdit`, `isRemoval`, `isEmpty` | Change flags. `isEmpty` means the fort has no name or description. |
| `isEditName`, `isEditDescription`, `isEditLocation`, `isEditImageUrl` (`isEditImgUrl`) | What was edited |
| `name`, `description`, `imgUrl` | Current values (the newest available) |
| `oldName`, `oldDescription`, `oldImageUrl` (`oldImgUrl`), `oldLatitude`, `oldLongitude` | Before the change |
| `newName`, `newDescription`, `newImageUrl` (`newImgUrl`), `newLatitude`, `newLongitude` | After the change |
| `latitude`, `longitude` | Fort location (**5.3+**) |
| `zoom`, `map_latitude`, `map_longitude` | Map position covering the old and new locations |
| `change_type`, `edit_types` | Raw webhook fields |

Names and descriptions are sanitised. Fort updates have no time fields.

## Greeting and Help (`greeting`, `help`)

These templates are shown when a user registers and when they run `!help`. They only get:

| Field | Description |
|---|---|
| `prefix` | The bot's command prefix (`!` or `/`) |
| `userIsAdmin` | `help` only: the user is an admin |

`{{prefix}}` exists **only** in these templates. On 5.3+ helpers in them use the user's language (`!help`, the `!poracle` greeting) or `[general] locale` (greetings sent by role reconciliation); on 5.2 they always use English.

## Button Responses (`buttonResponse`)

Button responses are rendered from the alert's [snapshot](dtsadvanced.md#snapshots), so they get the same fields as the alert the button was attached to. See [Interactive Buttons](dtsadvanced.md#interactive-buttons).
