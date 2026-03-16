---
title: !track
nav_order: 3
layout: default
parent: English
grand_parent: User Guide
---

# `!track` in Detail
This document provides details on the use of the `!track` command for Poracle. The `!track` command allows you to customize the notifications that you will receive from your server that you may tune with these commands to get the information tailored to suit your needs.

## Notes
* Some Pokémon are available from scanners without IV details. Poracle will treat these as having an IV of -1 in filtering decisions - specifiying a minimum IV of 0 (i.e. iv0) will exclude these. So to avoid having 2 notifications for a given Pokémon (one for the unscanned IVs and other with the IVs), specifiy an iv0 for it. If you don't care what its IV is and just want the Pokémon (to fill your Pokédex or get candy/XL), then leave the filter at iv -1, which is displayed as ?% in the notifications.
* If you do not specify a distance with the *d* filter, then the tracking will default to the areas that you have configured with `!area` command. The *d* filter only works if you have a `!location` set.
* Using a colon (:) in some filters is optional, but helps with non-English language processing.

# Command Options

| Filter    | Example                         | Description  |
|----------|-------------------------------|------------|
|           |`!track pikachu`<br>`!track 2 3 4`| Command used with no filters. This tracks the specified Pokémon within the area you are tracking in (see `!area` in [Options](options.md)). Tracking of Pokémon can be by name (e.g. Pikachu) or Pokédex number (e.g. 2). Ranges don't function for Pokémon numbers. For example `!track 2-4` will not work. You can use + to designate evolutions of the target Pokémon. For example: `!track 2+` would track Ivysaur and Venusaur. You can use Pokémon names with + as well. + will not function with `!untrack` |
|d          |`!track pikachu d750`             | Filter to track Pokémon within meters of your set location (see `!location` in [Options](options.md)). This example tracks Pikachu within 750 meters |
|iv         |`!track pikachu iv90`             | Filter for IV% of Pokémon. This example tracks Pikachu within the area you are tracking (see `!area` in [Options](options.md)) with a minimum IV of 90%. To set a maximum, use the range format, such as `!track pikachu iv90-99` |
|[male\|female\|genderless]|`!track rattata male`            | Filter for gender. This example tracks male Rattata |
|size:[xl\|xxl\|xs\|xxs]|`!track pikachu size:xxl`| Used to track Pokémon of XXL or XXS size. This example would track XXL Pikachu. To set a maximum, use the range format, such as `!track magikarp size:xxs-xs` |
|everything |`!track everything iv90 level20`  | Everything is a special name that includes all Pokémon. Using *everything* will not modify existing individual entries; rather, this will apply to all entries globally. Individual entry configurations overrule the *everything* entry. This example would track eveything with a minimum IV of 90% level 20 and higher |
|individually|`!track everything individually iv100` | Inserts a tracking entry for every single Pokémon rather than a single 'everything' entry. This allows you to do 'everything but..' by removing some afterwards|
|type       |`!track dragon`                   | The Pokémon type can be specified. This exmaple tracks dragon type Pokémon |
|gen        |`!track everything gen6 iv60`<br>`!track dark gen3 iv90` | *gen* is a filter so needs to be specified alongside *everything* or types lists of Pokémon. The first example tracks gen 6 Pokémon with a minimum IV of 60% and higher. The second example tracks dark-type Pokémon from gen 3 of IV 90% and higher |
|[atk\|def\|sta]|`!track larvitar pupitar tyranitar atk15` | Filter for minimum individual values. This example would track Larvitar family with 15 attack and anything for the other 2 IVs. To set a maximum, use the range format, such as `!track medicham meditite atk0-5 def13-15 sta13-15` |
|level      |`!track everything level:35`      | Filter for Pokémon level. This example tracks everything that is level 35 and higher. To set a maximum, use the range format, such as `!track everything level:20-35` |
|cp         |`!track slaking cp3000`           | Filter for minimum CP. This example tracks Slaking with a minimum CP of 3000. To set a maximum, use the range format, such as `!track gligar cp:0-1500` |
|form       |`!track growlithe formhisuian`    | Filter to apply to the specified Pokémon. See [Forms](forms.md) |
|t          |`!track pikachu t600`             | Filter for minimum time left before despawn measured in seconds. This example tracks Pokémon with 10 minutes left |
|rarity     |`!track rarity:6`                 | Minimum rarity (1-6) or common, uncommon, rare, very-rare, ultra-rare, unseen. To set a maximum, use the range format, such as `!track rarity:4-6` |
|clean      |                                  | Delete alarm after it expires |
|template   |                                  | Specify an alternate template (administrator will give you template IDs) |

## PVP
These commands are used to help find Pokémon that are useful in the Go Battle League (GBL). You can specify the Pokémon, league, CP, and best IV combination. The best IV combination is that which puts your Pokémon closest to the CP limit of a given league at the Pokémon's highest possible level, which will provide more HP and damage output. This normally means that the Atk value is low. When specifying this value, use the format of `league:topPvP`. The top PvP IV value are the combinations that you are looking for, such as `great10` will track Pokémon in the Great GBL League that have the top 10 best PvP IV combinations for that Pokémon.

|Command|Usage|
|---|---|
|`!track snorlax great1`               | Track top 1 PvP IV combination for Snorlax in the Great League |
|`!track everything great1 greatcp1450`| Track top 1 PvP IV combination Great League Pokémon maxing out at or above 1450CP for any Pokémon |
|`!track snorlax ultra100`             | Track top 100 PvP IV combinations for Snorlax in the Ultra League |
|`!track everything ultra1 ultracp2400`| Track top 1 PvP IV combination Ultra League Pokémon maxing out at or above 2400CP |
|`!track snorlax cap51`                | Depending on your server configuration, you can adjust the level cap for PvP IV combination consideratings for tracking. By default there is a cap of level 50. Some people configure their servers to calculate 51 as well (i.e. you have a best buddy), or level 40 (popular before the level cap) |

### Notes
* If you track a Bulbasaur, it will show the ranks for evolutions (e.g. Ivysaur), but that tracking rule won't make it track Venusaur itself (i.e. a wild spawn).
* You can directly track an evolution, like Ivysaur, and matching Bulbasaur will be found

An example notification containing PVP information - you can see what this Nidoran can become in Great and Little leagues in all its various forms. In the alert the display contains all the evolutions, if their rank/CP match.

Nidoran♀ 60.0% (0/14/13)<br>
Ends: 9:39:44 AM (18m 45s)<br>
CP: 353 | IV: 60.0% | Level: 18<br>
Size: M | Types: ☠<br>
Moveset: Poison Sting/Sludge Bomb<br>
Little:<br>
#1 Nidoran♀ @500CP (Lvl. 25.5/50)<br>
Great:<br>
#6 Nidoqueen @1498CP (Lvl. 23/50)<br>

In this example, the Drilbur itself is going to have a CP of 1500 for Great League, but its evolution will not fit into this league

#### Example Notfication
Drilbur♂ 88.9% (10/15/15)<br>
Ends: 9:50:00 AM (17m 28s)<br>
CP: 724 | IV: 88.9% | Level: 19<br>
Size: M | Types: ⛰️ | ☀ (Boosted)<br>
Moveset: Mud-Slap/Rock Tomb<br>
Great:<br>
#1 Drilbur @1500CP (Lvl. 49.5/50)<br>

# Deprecated
These commands can be used, but will not be supported as well as other commands.

| Filter    | Example                         | Description  |
|----------|-------------------------------|------------|
|weight     |`!track magikarp weight13130`     | Filter for the minimum weight of the Pokémon. This example tracks "big" Magikarp (13130 grams and higher) |
|maxweight  |`!track rattata maxweight2410`    | Filter for maximum weight of the Pokémon. This example tracks "tiny" Rattata (2410 grams and lower) |
|maxiv      |`!track pikachu maxiv0`           | Filter for maximum IV of Pokémon. This example tracks Pikachu with 0% IV only |
|maxsize    |`!track magikarp size:xxs-xs`     | Use !track size as a range instead. The example is such a case |
|[maxatk\|maxdef\|maxsta]|`!track medicham meditite atk5 def15 sta15` | Filter for maximum individual values. This example would track Meditite family for exactly 5/15/15, which is the best IV combination for GBL Great League |
|maxlevel   |`!track gligar maxlevel33`        | Filter for Pokémon maximum level. This example tracks Gligar that is level 33 and lower |
|maxcp      |`!track gligar maxcp1500`         | Filter for maximum CP. This example tracks Gligar with a maximum CP of 1500 |
|maxrarity  |                                  | Maximum rarity (1-6) or common, uncommon, rare, very-rare, ultra-rare, unseen |
