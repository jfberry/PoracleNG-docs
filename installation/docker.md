---
title: Docker Installation
nav_order: 2
layout: default
parent: Installation
grand_parent: v4
---

# Docker

Note: this is an alternative way to install PoracleJS and not needed if you choose to install it with the [manual installation guide](manual).

Disclaimer: This page is using the development branch. Relatively fewer people are using Docker with
Poracle, so if you run into trouble please ask for help. 
If you want to use the master branch, change the image tag in the compose file and adjust the urls of the wget commands.

## Prerequisites

Make sure to have [Docker](https://docs.docker.com/get-docker/) and [docker-compose](https://docs.docker.com/compose/install/) installed.

## Installation

You can just copy & paste this to do what is written below:

```bash
mkdir PoracleJS-docker 
cd PoracleJS-docker 
wget -O docker-compose.yml https://raw.githubusercontent.com/KartulUdus/PoracleJS/develop/docker-compose.yml.example
mkdir config && wget -O - https://github.com/KartulUdus/PoracleJS/archive/develop.tar.gz | tar xz -C config/ --strip=2 "PoracleJS-develop/config/"
```

This will:

1. Create a directory `PoracleJS-docker`.
2. Create a file `docker-compose.yml`.
3. Create a directory `PoracleJS-docker/config` and download the content of it from GitHub.


### Editing the docker-compose file

This compose file defines two containers: `poracle_db` and `poracle`.

### poracle_db

This is just a normal MariaDB container for storing all the filter data. It will create a directory called `poracledb` on the first startup. Thats where all the data is stored. Do not delete it!  
**Make sure to adjust the database credentials in the environment section!** Note: They will be stored in the `poracledb` directory and adjusting them after the first launch will not change them!

### poracle

This is the actual poracle container. Do not adjust `PORACLE_SERVER_HOST`, `PORACLE_SERVER_PORT`, or `PORACLE_DB_TYPE`! If you want to adjust the port used by PoracleJS, do that in the `ports` section, not in the `environment` section. Make sure to change **just** the value on the left side of the colon.  
`PORACLE_DB_DATABASE`, `PORACLE_DB_USER` and `PORACLE_DB_PASSWORD` should be the same as for the `poracle_db` container.

If you want to test a specific pull request you need to build PoracleJS locally. Comment the `image:` line and uncomment the two lines above. You obviously need to have a cloned PoracleJS version in the same directory as the compose file to successfully build the image. To switch the branch, change the image tag from `develop` to the branch name.

## Running

Make sure to be in the `PoracleJS-docker` directory for all commands below. You can always check the state of all running containers by typing `docker ps`.

### Starting PoracleJS

This will start the two containers in detached mode so they can run on thier own without an active shell. The second command is to see the current logs. 
`docker-compose up -d && docker-compose logs -f`


### Stopping PoracleJS

`docker-compose down`

### Updating PoracleJS

This will pull the latest docker image from GitHub and restart the poracle container. You may want to compare the `config/` directory at GitHub with yours to see if there was any change. 
`docker-compose pull && docker-compose up -d`

Congratulations! PoracleJS has been installed but we still need to configure the services to work together. Let's move onto the [configuration](./configuration) next.
