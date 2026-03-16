---
title: Weather forecasting
nav_order: 9
layout: default
parent: Configuration
---

# Introduction

Poracle supports advanced tracking of in-game weather including:
* Weather change alerts from Weather Webhooks
* Weather change alerts from boosted Pokémons in the s2cell

<img src="../assets/weather-change-example.png" style="width: 450px">

* Weather Forecast based on AccuWeather data to show on Pokémon alerts if the weather changes are likely to change boost status and stats

<img src="../assets/weather-forecast-example.png" style="width: 450px">

## Configuration

```json
  "weather": {
      "weatherChangeAlert": false,                  // To enable or disable the weather change alerts
      "showAlteredPokemon": false,                  // Track weather changed pokémon to be able to be shown in DTS
      "showAlteredPokemonStaticMap": false,         // Show weather changed on static map
      "showAlteredPokemonMaxCount": 10,             // Max number of changed pokémon allowed per alert
      "enableWeatherForecast": false,               // To enable or disable weather forecast (using accuweather)
    // AccuWeather API keys - array of keys, poracle will rotate through keys
      "apiKeyAccuWeather": [""],
      "apiKeyDayQuota": 50,                         // Maximum API calls allowed per key per day
      "smartForecast": false,                       // use smart update of weather forecast (pull on demand if no weather data available for a given cell, otherwise will wait for next standard refresh)
      "localFirstFetchHOD": 3,                      // First hour of the day for the first API call (3am local seems best)
      "forecastRefreshInterval": 8                  // API call interval
  },
```

## Forecasting

Register on AccuWeather website and create an APP which will grant you an API key. You can make 50 calls per day with one key. Depending on the interval you'll set to fetch forecast data, this can cover around 15 level 10 s2cells per day (with getting weather every 8 hours)
