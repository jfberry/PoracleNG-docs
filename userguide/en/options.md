---
title: Filtering Options
nav_order: 3
layout: default
parent: English
grand_parent: User Guide
---

# Filtering Options

## Basics

### Simple commands

When you are talking to Poracle there are a few basic commands you should remember

Command | Description
--- | ---
**!help** | Replies with some help text
**!tracked** | Display a summary of everything you are currently tracking
**!stop** | Stop sending me messages - if you have tracked too much this can be a good way to stop Poracle spamming you!
**!start** | Start sending messages again
**!info** | Display details for a given collection, such as rarity, items, or Pokémon

Poracle will temporarily stop sending you notifications if you receive too many over a short period of time.  If you
do this repeatedly then it will stop sending you any notifications at all. Usually you can fix your tracking and
issue a *!start* command to resume, but you may be asked to contact your site administrator.

### Notification area

You will only receive notifications for things that happen within the areas you have opted in, _or_ within a certain 
distance from a specific location you set.

When you first start to use poracle, it is sensible to use the pre-defined areas

### Tracking by areas

**`!area list`** will show you a list of all the areas that are available to you.  You can add tracking in these
areas using **`!area add`** and remove them with **`!area remove`**.  You can see the areas, and every other detail
about your tracking using `!tracked`

The area command helpfully can show you the extent of an area using the `!area show` command.

### Setting your location

You can set your home location and ask for notifications only within a certain distance of that. You do this by specifying !location -

eg `!location 10 New Dover Road, London SW3 4SX`

or find the latitude and longitude of your address in google maps and set it directly

eg `!location 51.279,1.080`

Once you have specified your location you will then be able to a distance marker to any notifications by using the `d` flag - for example `!egg level5 d500` - to see things within 500m of your house

### Distance guide

How far/fast can you walk? Here is a guide from the internet (so it must be true!)

Meters | Fast | Moderate | Easy Walk
---|---|---|---
1000   |   7   |    10    |     13
2000   |   14  |    20    |     25
3000   |   21  |    30    |     38
4000   |   28  |    40    |     50
5000   |   35  |    50    |     63

**`!tracked`**  
Shows the current filtering settings, followed by specifications on the location, the selected areas and all filters referring to anything from [Pokémon](#pokémon) to [Rocket/Invasion](#rocketinvasion).

## Things you can be notified about!

## Pokémon

There are a lot of options for filtering your alerts to get the Pokémon you want.  The [Track](track.md) page contains all the detail, this just describes the
basics.

Let's deconstruct this command.
**`!track pikachu d1000 iv20`**

**`!track`**  
All commands to receive Pokémon notifications start with "!track". Using "!untrack [...]", you can remove the respective commands again. 

**`pikachu`**  
This part of the command indicates which Pokémon the command refers to. It is possible to name one or more Pokémon using their Pokédex numbers or name (multiple languages are supported if your Poracle is configured for this), separated by space. You can track all Pokémon using "everything". You can track all Pokémon of a particular type - eg `!track dark` and filter Pokémon using a generation - eg `!track everything gen1` or `!track dragon gen3`.

**`d1000`**  
If you do not specify a *'d' (distance) filter, then the areas you have selected using the !area command will be used to filter these alerts. If you do specify a distance, then you will be notified if a Pokémon falls within the area around your location.

You can see what a particular distance radius looks like for you by doing `!area show d1000`, for example.

**`iv20`**  
"iv##" indicates the minimum IVs the Pokémon must have to warrant a notification. If you do not set an "iv##", then no minimum will be used. There are other options including "maxiv##" (deprecated) indicates the maximum IVs a Pokémon may have to warrant a notification. An iv of -1 is used when the IV is not known, so in this case specifying a minimum iv of 0 may be advisable too. See [Track](track.md) for more details.

Examples:  
**`!track pikachu iv100`** - sends notifications for all Pikachu with 100 IV. 
**`!track pikachu iv0 maxiv0`** - sends notifications for all Pikachu with 0 IV.  
**`!track pikachu miniv50 maxiv60`** - sends notifications for all Pikachu with IVs between 50% and 60%.
**`!track pikachu iv50-60`** - same as above, but uses range notation

**Note:** IV specifications will be rounded to whole numbers. A Pokémon classified as IV98 actually has 44 of 45 possible IV points (15atk,15def,15sta), which are 97.77% mathematically. Since, IV97.77 should not be included in the IV range 98 you would be best filtering for IV97 to capture all IV98 Pokémon.

Also note that `iv100` and `iv:100` are the same filter and using the colon may help process non-English language commands better.

You can specify more filters (eg levels, atk, def, sta) - see [Track](track.md) for more details.

Additionally to the Pokémon names, Poracle also knows various names for specific forms, e.g. to look only for Alola-Pokémon. You can find an explicit list in the section [Pokémon Forms](#special-form_names).

You can create several entries for a single Pokémon. 
For example, you could use two commands to receive notifications for Pokémon with maximum IV0 and Pokémon with minimum IV100. 

Important: to check if a !track command works as intended, use the command **`!tracked`** whenever you need to. This way, the bot will show you all filter settings it is currently using. 

## Quests
All quest commands start with **`!quest`** and can be removed again using **`!quest remove`**. Quest commands work within areas or distances as described above.

### Pokémon
**`!quest Pikachu`** - sends notifications for all quests with Pikachu as a reward. 

### Items  
**`!quest everything`** - sends notifications for all quests.
**`!quest all_items`** - sends notifications for all item reward quests.
**`!quest all_pokemon`** - sends notifications for all Pokémon reward quests.
**`!quest rare_candy`** - sends notifications for all quests with rare candy as a reward.  

Use `!info items` to get an up-to-date list of avaialble items. The table below may not be up-to-date.

| Written Command | Item Name |   
|:-----------|:-------------|  
|Poke_Ball|  Poké Ball |  
|Great_Ball  | Great Ball |  
|Ultra_Ball  | Ultra Ball |  
|Master_Ball  | Master Ball |  
|Premier_Ball  | Premier Ball |  
|Potion  | Potion |  
|Super_Potion  | Super Potion |  
|Hyper_Potion  | Hyper Potion |  
|Max_Potion  | Max Potion |  
|Revive  | Revive |  
|Max_Revive  | Max Revive |  
|Lucky_Egg  | Lucky Egg |  
|Incense  | Incense |  
|Lure_module  | Lure Module |  
|glacial_lure_module  | Glacial Lure Module |  
|mossy_lure_module  | Mossy Lure Module |  
|magnetic_lure_module| Magnetic Lure Module |  
|Razz_Berry  | Razz Berry |  
|Nanab_Berry  | Nanab Berry |  
|Pinap_Berry  | Pinap Berry |  
|Golden_Razz_Berry  | Golden Razz Berry |  
|Silver_Pinap_Berry  | Silver Pinap Berry |  
|egg_incubator_∞ | Egg Incubator Unlimited |  
|egg_incubator  | Egg Incubator |  
|super_incubator  | Super Incubator |  
|Sun_Stone  | Sun Stone |  
|King's_Rock  | King's Rock |  
|Metal_Coat  | Metal Coat |  
|Dragon_Scale  | Dragon Scale |  
|Upgrade  | Up-Grade |  
|Sinnoh_Stone  | Sinnoh Stone |  
|Unova_Stone  | Unova Stone |  
|Fast_TM  | Fast TM |  
|Charged_TM  | Charged TM |  
|Elite_Fast_TM | Elite Fast TM |
|Elite_Charged_TM | Elite Charged TM |
|Rare_Candy  | Rare Candy |  

**Note:** The system only supports English names for quest items. You can select all items available in the game data. This does not mean, however, that all those items are actually available in quests or at all in the game.

### Stardust
**`!quest stardust`** - all quests with stardust as a reward.
**`!quest stardust500`** - all quests with at least 500 stardust as a reward. 

### Mega Candy
** `!quest energy`** - all quests with mega energy as a reward
** `!quest energybulbasaur` ** - all quests with bulbasaur mega energy

## Raids
There are two different kinds of Raid notifications. You can receive notifications when Raid Eggs first appear or when a certain Raid Boss hatches. 
For Eggs, your command starts with *!egg*, while the command for the Raid Boss starts with *!raid*.
Consequently, you have to use *!raid*-commands to receive notifications for specific Pokémon.

**`!raid Snorlax`** - sends notifications for all Snorlax Raids. 

Additionally, you can set the following filters for *!raid*-commands and *!egg*-commands (most of the filters can be combined freely):

**`!raid(!egg) level5`** - sends notifications for all Tier 5 Raids or Eggs (cannot be combined with a filter for specific Pokémon!) Note level 6 is for mega raids!

**`!raid(!egg) d1000`** - Raids can be filtered by location.

**`!raid(!egg) ex`** - sends notifications only for Raids at Gyms that are currently labeled as EX Raid Gyms.

**`!raid(!egg) instinct/valor/mystic/harmony`** - sends notifications only for Raids at Gyms currently controlled by the selected team.

**`!raid(!egg) everything`** - sends notifications for all Raids/Eggs.

**`!egg remove`** or **`!raid remove`** removes all filters for Raids and Eggs. It can also refer to single Raid Tiers and Pokémon, e.g **`!raid remove Snorlax`**.

## Rocket/Invasion
Currently, scanning Rocket-invaded PokéStops only happens passively. This means that the PokéStops are not actively approached, just detected by the radar. At any point where a scanner is doing a job of any kind, e.g. scanning a Pokémon, it collects information about invaded Stops in the surrounding area.

Included is information on the location of the grunt, their quote, and their gender. There is a specific set of Pokémon assigned to every combination of quote and gender. This set consists of different Pokémon which can be encountered as invasion reward at different odds.

Since the workers never actively engage in a Rocket battle, they do not know exactly which Pokémon will be the reward. Consequently, you cannot filter for specific Pokémon in Poracle, but only for type and gender.

All invasion-commands start with *!invasion*. 
You can remove all filtering settings for invasions using **`!invasion remove everything`**. 

**`!invasion everything`** - sends notifications for all invaded Stops. 

*!invasion*-commands can be specified by both areas and distances (d). 

Additionally, you can filter by all 16 available Pokémon types and gender.

| Written Command | Meaning/Result |   
| :-----------|:-------------|  
|normal| Tracks invasions with a chance to get a normal type Pokémon  |  
|fire| Tracks invasions with a chance to get a fire type Pokémon  |  
|fighting| Tracks invasions with a chance to get a fighting type Pokémon  |  
|water| Tracks invasions with a chance to get a water type Pokémon  |  
|flying| Tracks invasions with a chance to get a flying type Pokémon  |  
|grass| Tracks invasions with a chance to get a grass type Pokémon  |  
|poison| Tracks invasions with a chance to get a poison type Pokémon  |  
|electric| Tracks invasions with a chance to get an electric type Pokémon  |  
|ground| Tracks invasions with a chance to get a ground type Pokémon  |  
|psychic| Tracks invasions with a chance to get a psychic type Pokémon  |  
|rock| Tracks invasions with a chance to get a rock type Pokémon  |  
|ice| Tracks invasions with a chance to get an ice type Pokémon  |  
|bug| Tracks invasions with a chance to get a bug type Pokémon  |  
|dragon| Tracks invasions with a chance to get a dragon type Pokémon  |  
|ghost| Tracks invasions with a chance to get a ghost type Pokémon  |  
|dark| Tracks invasions with a chance to get a dark type Pokémon  |  
|steel| Tracks invasions with a chance to get a steel type Pokémon  |  
|fairy| Tracks invasions with a chance to get a fairy type Pokémon  |  
|mixed| Tracks invasions with an undetermined type | 
|male| Tracks male invasions  | 
|female| Tracks female invasions  | 
|giovanni|Tracks for giovanni|
|arlo|Tracks for arlo|
|cliff|Tracks for cliff|
|sierra|Tracks for sierra|
|decoy|Tracks for decoy grunts|
|mixed|Tracks for invasions with a mixed type |

*Note:* You can combine several filters/types. E.g.: **`!invasion fire water ice male`** - tracks all male invasions with either a fire type, water type, or ice type Pokémon as a reward.

### Lures

Track when lures have been placed on pokestops

eg: **`!lure mossy`**

|normal|
|magnetic|
|grassy|
|mossy|

### Nests

If your administrator has configured it, ...

## Advanced

Command | Description
--- | ---
**!profile** | Create and update a profile for your tracked settings. See [Profiles](profiles.md).
**!script** | *Documentation pending*
