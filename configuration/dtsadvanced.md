---
title: DTS Templates (Advanced)
nav_order: 5
layout: default
parent: Configuration
---

{% raw %}
## Handlebars functions

### {{numberFormat xx 2}} or {{toFixed xx 2}}

Format a number to a given number of decimal places

### {{calculateCp}}

{{calculateCp baseStats 20 15 15 15}}

### {{pokemonBaseStats}}

{{calculateCp (pokemonBaseStats 2) 20 15 15 15}}

### {{getPowerUpCost}}

`{{getPowerUpCost}}` will give a text string

or can be used in it's constituent parts eg:

```
{{#getPowerUpCost 5 10}}Candy - {{candy}} Stardust - {{stardust}}{{/getPowerUpCost}}
```

```
{{#getPowerUpCost ../level this.level}}:stardust:{{addCommas stardust}}:rarecandy:{{candy}}{{#compare xlCandy '>' 0}}:rarecandyxl:{{xlCandy}}{{/compare}}{{/getPowerUpCost}}
```

<img src="../assets/get-powerup-cost-example.png" style="width: 450px">

### {{concat}}

`{{concat pokemonId '_' formId}}` would give 5_0

### {{pad0}}

`{{pad0 pokemonId}}` would pad to three digits eg 021

### {{map}}

Poracle contains a generalised mapping function.  Define a map in the customMaps folder like this --
```json5
{
  "name": "raidCounters",
    "map": {
        "144": {
            "trainers": "2",
            "counters": "Rampardos, Shadow Tyranitar, Rhyperior, Terrakion, Shadow Omastar & Tyranitar"
        },
        "145": {
            "trainers": "3",
            "counters": "Shadow Mamoswine, Shadow Tyranitar, Rhyperior, Shadow Mewtwo, Mega Abomasnow & Mamoswine"
        },
        "146": {
            "trainers": "2",
            "counters": "Shadow Tyranitar, Rampardos, Rhyperior, Terrakion, Shadow Omastar & Shadow Aerodactyl"
        }
    }
}
```
And you can then use it in your DTS to map from anything to anything - eg in this example pokemon Id maps to trainers and the counters for advanced raid displays.  You use it like this: `{{#map 'raidCounters' pokemonId}}Trainers needed: {{trainers}} Best counters: {{{counters}}}{{/map}}`. If you have a single value you map to you can just use `{{map 'name' value}}` rather than having subvariables.  

I hope people will come up with some imaginative mappings and share them (we have a defaults/customMaps folder so we can ship some cool ones!)

## handlebars helpers

### {{if}}

`{{#if verified}}Verified{{else}}Not verified{{/if}}`

### {{#each}}

eg `{{#each matched}}{{map 'arealist' this}}{{/each}}`
to use a map for each area name to more descriptive text

### {{compare}}


### {{join}}

### {{eq}}

{% endraw %}