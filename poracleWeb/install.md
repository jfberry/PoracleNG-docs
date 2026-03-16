---
title: Installation
nav_order: 1
layout: default
parent: Poracle Web
---

### Installation

1. Clone the repo
   ```sh
   git clone https://github.com/bbdoc/PoracleWeb.git
   ```
2. Install NPM packages
   ```sh
   npm install
   ```
3. Copy `config_example.php` to `config.php` and adapt to your needs
4. Have a Web Server pointing to your install directory (This tool doesn't include any standalone WebServer)

5. You will need to configure your some basic settings in config.php:
- Access Parameters to your Poracle DB
- Admin ID. From discord, right click on your avatar and use "Copy ID". This defines the ID of the administrator who will be able to perform further configuration tasks.
- Discord Bot. If you use PMSF, you can reuse the same parameters for `discordBotClientId` and `discordBotClientSecret` or find them on the [Discord application Portal](https://discord.com/developers/applications). `redirect_url` should point to your PoracleWeb base directory and should be configured as a Redirects in your Discord bot.

For those parameters go to :
- [Discord application Portal](https://discord.com/developers/applications)
- Select your Bot (or create a new one).
- Go to OAuth2 and add your `https://yourdomain.com/discord_auth.php` (`https://yourdomain.com`) being your `redirect_url`
- Client ID can be found under "General Information"
- Client Secret can be found under "General Information" by clicking the "Click to reveal" link.A


### Configurating API in Poracle

```json
  "server": {
      "host": "127.0.0.1",        // host name to listen on 127.0.0.1 only localhost; 0.0.0.0 would be in all network interfaces
      "port": "3030",             // port
    // ipWhitelist, ipBlacklist - array of whitelisted or blacklisted addresses
      "ipWhitelist": [],
      "ipBlacklist": [],
      "apiSecret": ""             // apiSecret to use for access to Poracle API -- blank API disabled
  },
```

### Testing the API

To test the API: 
```bash
curl -i -H "X-Poracle-Secret: whatever" http://localhost:4201/api/config/poracleWeb
```

Replace the secret, address and port with your own

6. Go to your PoracleWeb site and head to Admin Tools / Server Settings for additional configuration steps and parameters. You need to be logged in as an admin (as defined by `admin_id` par
   ameter to be able to access this page.
