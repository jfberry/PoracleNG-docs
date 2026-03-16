---
title: Manual Installation
nav_order: 1
layout: default
parent: Installation
---

# Manual Installation
In this section we will discuss how to perform a manual installation. Some requirements may link to other installation guides since we do not want to re-invent the wheel.

## Prerequisites
PoracleJS requires several prerequisites to operate. The following prerequisites must be installed for PoracleJS to function properly.
 * [NodeJS](https://nodejs.org/en/). Poracle requires Node 16.x
 * [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
 * A databases is required for PoracleJS to operate properly. Only one needs to be configured for PoracleJS to operate correctly.
    * [MariaDB](https://www.digitalocean.com/community/tutorials/how-to-install-mariadb-on-ubuntu-20-04)
    * [MySQL](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-20-04) [MariaDB > MySQL :)]
   
### Notification Service
We will need to configure a Discord or Telegram bot to allow PoracleJS to send messages to the required service. Only one bot is required for the configuration. PoracleJS will require the token for the user regardless of the service. This will be highlighted in the individual pages on how to obtain that information.

 * [Discord Bot](../discordbot.html)
 * [Telegram Bot](../telegrambot.html)

### Database
A database and user need to be configured for PoracleJS to access the database. While you can reuse existing users it is recommended to create a new user for each application.

#### MariaDB / MySQL
Below are examples of how to create the database and user. Please make sure to use a [strong password](https://passwordsgenerator.net/) for the user. The example password is `poraclePassword`.
If PoracleJS connects to the database locally, you can give it only local access rights:
   ```sql
   CREATE DATABASE poracle;
   CREATE USER 'poracleuser'@'localhost' IDENTIFIED BY 'poraclePassword';
   GRANT ALL PRIVILEGES ON poracle . * TO 'poracleuser'@'localhost';
   exit
   ```
   
Alternatively, you can grant your user access from anywhere. This allows for remote connections regardless of the originating IP / hostname. Unless you have a need for remote access you should not allow the user to connect from any address.
   ```sql
   CREATE DATABASE poracle;
   CREATE USER 'poracleuser'@'%' IDENTIFIED BY 'poraclePassword';
   GRANT ALL PRIVILEGES ON poracle . * TO 'poracleuser'@'%';
   exit
   ```

### Known Issues
Depending on your MariaDB version you may run into an issue during the PoracleJS setup with the issue "Specified key was too long; max length is 767 bytes". If you encounter this issue you can review the [Configuration](./configuration#database) page for more information.

## Installing PoracleJS
Now that the prerequisites have been installed we can begin installing PoracleJS. There are a few more (optional, but highly recommended) steps to help reduce any potential security vulnerabilities.

### Security Considerations
Before we start the installation, we should decide on a user and installation directory to use. The $HOME directory may be the simplest solution, but the *nix conventions we should use `/opt` for unbundled packages (packages not part of the OS distro). We should also not use the user `root` for the installation.

#### Creating a User
We should never install software or run programs as `root`. This is a security hole that is easy to avoid. You can read more about installing as root [here](https://bencane.com/2012/02/20/why-you-should-avoid-running-applications-as-root/). If you already have a user you can skip this step.

### Check version of node
```bash
node -v
```

This should return node 16.

### Installation
1. Ensure you are running as the correct user. If not, switch to that account with `su <username>`. An example would be `su pogo` to switch the account `pogo`.
2. Ensure you are in the directory where you would like Poracle to be installed. 
3. The source of PoracleJS is hosted on GitHub. We will need to clone the repository with git into our current directory (specified by the `.` at the end of the clone).
   ```bash
   git clone https://github.com/KartulUdus/PoracleJS.git 
   ```
4. Change into the Poracle directory
   ```bash
   cd PoracleJS
   ```
5. Install PoracleJS requirements
    ```bash
    npm install
    ```
6. Clone the configuration to be used later
    ```bash
    cp config/default.json config/local.json
    ```
   
# Updating
To update the manual installation, you must perform a `git pull` in the correct directory as the installed user. If you perform a `git pull` with a user other than installed user you will run into permission errors.

An example update set of commands:
```bash
git pull
```

# Auto-Starting
There are a few services that allow for easy management of a process. The easiest to use is [pm2](https://pm2.keymetrics.io/). Since we never added the user `pogo` to sudoers we will be unable to run that command. If that is the case, exit back out to root and run the command. Once installed switch back to the user created for running PoracleJS. Utilizing pm2 for auto-startup requires running the following commands:
```bash
npm install pm2 -g
pm2 start poracle.js --name poracle 
```

The following commands are useful for pm2
```bash
pm2 stop poracle # Stop PoracleJS
pm2 start poracle # Start PoracleJS
pm2 restart poracle # Restart PoracleJS
pm2 logs poracle # Show the last 15 logs for PoracleJS
pm2 monit # ASCII Dashboard for monitoring pm2
```


Congratulations! PoracleJS has been installed but we still need to configure the services to work together. Let's move onto the [configuration](./configuration) next.
