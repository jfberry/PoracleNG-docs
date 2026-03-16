---
title: Basic Configuration
nav_order: 3
layout: default
parent: Installation
---
# Configuration
Since PoracleJS requires several services we need to configure them to work together. Some sections will not apply to the Docker configuration, and they will be identified as such. We will go over a basic configuration to get PoracleJS running. After all changes are made to the file the PoracleJS process will need to be restarted to take effect.

## Firewall
Opening the firewall should only be required on a select-few environments. The use-case for this would be that PoracleJS is required to be accessible on the internet. This is not required for local installations. If you are opening PoracleJS to the internet ensure you are using a reverse proxy for SSL-encrypted traffic. This has the added benefit of not exposing PoracleJS directly to the web. The other use-case would be if your environments default rule is drop. This is not the case for most installations.

PoracleJS runs on 3030/tcp by default unless a different port is selected. Ensure you open this port if its required.

## Geofence
The geofence file can be found at `./config/geofence.json`. The geofence help define custom areas. While this is not required it can help allow for multiple notification areas to be defined to pinpoint notifications. The easiest way to configure geofences is through the tool [Fence Editor](http://geo.jasparke.net/).
 * For each area click on `Create` in the top-left corner and fill out the name. This name cannot contain hypens (`-`) but can contain spaces. 
 * Fill out the field `Fence Name` and click on `Create` to begin drawing the fence
 * Click where the vertices should be for the area. Once you are done with the area, ensure you click the starting location to close the fence
 * Perform these steps for all areas you wish to include. Once you have created your areas click on `Save` and `Click to Download`

The downloaded file will be your geofence file. You can either copy the contents into the file on the server or upload it. If you are uploading it ensure the file is owned by the user running PoracleJS.


## Database
Depending on your MariaDB version you may run into an issue during the PoracleJS setup with the issue "Specified key was too long; max length is 767 bytes". If you encounter this issue it can be solved by updated the configuration and running a few commands. To see how to solve this problem review the [StackExchange article](https://dba.stackexchange.com/questions/231219/mariadb-10-1-38-specified-key-was-too-long-max-key-length-is-767-bytes).


## Configuration File
Both installation methods should have a file located at `./config/local.json`. This file will need to be modified to ensure the services are connected together properly.

### Database
```plain
This step can be skipped for Docker installations
```
The first configuration we need to make is to the Database. This section can be found under the key `database`. Make the following changes to this section:
 * client
    * mysql - For use with MariaDB / MySQL databases
 * conn
    * host - Hostname or IP of the database service. If hosting locally you can use `127.0.0.1`.
    * database - Name of the database during configuration. The example uses the database name `poracle`.
    * user - Username of the account for the database connection. The example uses the username `poracleuser`.
    * port - Port of the database service. MariaDB and MySQLs default port is `3306`.

### Configurating your scanner

See [Scanner](scanner.md) scanner

### Notification Service
You only need to configure the section for your notification service. Each page will go in-depth on how to configure the required elements for receiving data from PoracleJS.
 * [Discord](discord)
 * [Telegram](#telegram)

### Geocoding
If you wish to utilize maps as part of your notifications you need to configure the section `geocoding`. More information can be found on the [Configuration File](../config_file#geocoding) page.

### Validating the file
After making changes to the file we should ensure it is valid JSON. If it is not valid JSON PoracleJS will not start. The default format resembles cJSON which does not have an online linter. You may be able to put it into your browsers developer tools to ensure it is valid JSON.

PoracleJS has now been configured with the required basic information to run. You may want to tinker additional settings but they can all be found on the [Configuration File](../config_file) page.
