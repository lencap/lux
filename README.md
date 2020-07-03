# lux
Lux is a basic `docker-compose.yml` configuration to set up a [Source Control Management system](https://en.wikipedia.org/wiki/Version_control), like [Github](https://github.com), using [Gitea](https://gitea.io/en-us/), MySQL, and [Traefik](https://containo.us/traefik/). This work is just an extension of the intructions detailed at https://docs.gitea.io/en-us/install-with-docker/

Also included is the alternate `docker-compose-base.yml`, which is an even more basic configuration that sets up the following:
* Basic Traefik TLS setup using [LetsEncrypt](https://letsencrypt.org/) certs with DNS challenge
* DNS hosted at [DigitalOcean](https://www.digitalocean.com/)
* Traefik API and Dashboard with basic HTTP auth login
* Sample test myapp

This base configuration can be used to front any other application with __Traefik__. 

## Prerequisites
* Set up an Ubuntu 18.04 server, and install Docker and Docker-Compose
* Checkout this repo and cd to `lux` directory
* Create an `.env` file and populate the following parameters _to your liking_:
```
DO_AUTH_TOKEN=49a8b75d-Use-Your-Own-Digital-Ocean-Token-58b75d49a31fd31fdc3cc6
LE_EMAIL=postmaster@mydomain.com
TRAEFIK_DOMAIN=traefik.mydomain.com
SITE_DOMAIN=code.mydomain.com
DB_NAME=gitea
DB_USER=gitea
DB_PASSWD=gitea
MYSQL_ROOT_PASSWORD=gitea
MYSQL_USER=gitea
MYSQL_PASSWORD=gitea
```

## Getting started
To set up __Lux__, run:
```
docker-compose --verbose up -d
```

## Configuration
Once the system is running, go to your domain https://code.mydomain.com, click _Sign In_ and do the final system configuration.

The Traefik Dashboard should be available at your domain https://traefik.mydomain.com.

## SSH
The web UI access is easy enough via the browser, but the more popupar SSH access should also be available using user `git`, on your domain `code.mydomain.com`, and over port `2222`. To simplify this access, it's easier to update your `$HOME/.ssh/config` file with a stanza such as this:
```
Host                     code.mydomain.com
  Port                   2222
  User                   git
  IdentityFile           /home/user/.ssh/id_ed25519
  StrictHostKeyChecking  no
  UserKnownHostsFile     /dev/null
```

## Existing SSL certificates
Both docker-compose files have optional, commented out, sections to allow the use of existing SSL certs. This may be handy if you're not interested in using the  LetsEncrypt automation, or maybe you have your own certs.   

## Backup
TODO: Describe best way to backup the system

## Upgrade
TODO: Describe upgrade process
