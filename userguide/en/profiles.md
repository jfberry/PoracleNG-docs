---
title: Profiles
nav_order: 3
layout: default
parent: English
grand_parent: User Guide
---

# Profiles
Profiles allow you to specify different tracking configurations for different times.
You create profiles with `!profile add` eg `!profile add home`.  You can list your profiles with `!profile list`

Note that to change tracking switch to a particular profile (eg `!profile home`, issue `!track`, `!location` or `!area` commands and these will be set to the current profile.

But what about automatic change based on time of day?  You can do 
`!profile settime` _activationtimes_ - where the activationtimes are 
days of week & times to activate - eg `mon9:15` or `weekday17:00` or 
`weekend09`.

Every 10 minutes Poracle will check if a profile was due to activate and will set it.

Note that the `!start` and `!stop` commands are global for all profiles.

## Command Definitions

Command|Description
---|---
`!profile add <name>`|This will add a new profile with the name provided. If you currently have no profiles then your current !tracked settings will be applied to this profile. If you have an existing profile then this command will create a blank profile. If you want to copy the settings from one profile to another then use the `!profile copyto <name>`.
`!profile <name>`|This will switch to the profile with the name provided.
`!profile copyto <name>`|This will copy the profile settings of the currently selected profile to the profile name provided. This will not copy settime configuration settings.
`!profile list`|This will list the available profiles, displaying which is currently selected with an asterisk (*) by the number associated with the profile as well as when the profile becomes active.
`!profile settime <timestring>`|This will apply a time setting to the current profile for when this profile will become active. Change to a different profile with `!profile <name>' to apply time settings to a different profile.
`!profile rename <name>`|This will remove a profile of the name provided.

## Scenario 1: Turning off notifications at night
In this scenario, we want to get alerts after our working hours up until we are done gaming for the day. Assuming that we work until 5 PM and stop gaming at 9 PM. Weekends are gaming potential from 8 AM to 8 PM.

### Setup home profile
Create a new profile.

`!profile add home`

Select it.

`!profile home`

At this point we want to configure our profile with `!location`, `!area`, `!track`, `!raid`, `!quest`, etc.

### Set the start time for the profile
Since the bot could change profiles 10-20 minutes after the desired scheduled time, we will change to our active profile at 4:40 PM. That way, if there is something close by or work we can jump on it after we leave the door.

`!profile settime weekday16:40 weekend07:40`

This profile will now become active sometime after 4:40 PM on weekdays and sometime after 7:40 AM on weekends.

### Create a blank profile
This will create a blank profile named 'off'.

`!profile add off`

### Set the time to turn off tracking
Select the off profile.

`!profile off`

This will select the 'off' profile then configure it to become active sometime after 9 PM on weekdays and 8 PM on weekends.

`!profile settime weekday21:00 weekend20:00`

Because the 'off' profile contains no tracking configuration, there will be no notifications.

### Go back to your home profile to get notifications
`!profile home`

## Scenario 2: Including a commute
In this scenario, you got a new job in a new area and want to be able to track stuff around your building during lunch. Your job is a couple of cities away, so you don't need to get notifications of what you could have had if you were at home because that hurts.

### Set up work profile
Create work profile.

`!profile add work`

Select home profile.

`!profile home`

Copy the home profile configuration with all tracking to the work profile. Activation times will not be copied.

`!profile copyto work`

Select work profile.

`!profile work`

The ##.##,##.## is where you would place the coordinates of your work building. e.g. `!location -12.34,56.78`

`!location ##.##,##.##`

Add the city of your work.

`!area add newcity`

Remove the city of your home.

`!area remove homecity`

Configure any tracking that you want to do while at work. You may want to use distance filter for `!track` so that the notifications are close enough for you to act upon.

You may not have time to go raiding durin lunch, so perhaps just keep it to rare Pokémon.
 
`!raid remove everything`

Set the work profile to start sometime after 11:40 AM.

`!profile settime weekday11:40`

### Update your off profile
Select the off profile.

`!profile off`

Include all of the times that you want the profile to become active. In this case, we added 1 PM to what we had configured previously for 9 PM on weekdays and 8 PM on weekends.

`!profile settime weekday13:00 weekday21:00 weekend20:00`

### Go back to your home profile
`!profile home`

Now we want our home profile to become active when we finish our commute home, which we'll say is by 5:30 PM. We'll set the time to 5:10 so that some time after that it will become active.

`!profile settime weekday17:10 weekend07:40`
