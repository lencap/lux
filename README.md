# lux
Lux is a basic [Source Control Management system](https://en.wikipedia.org/wiki/Version_control), like [Github](https://github.com), using [Gitea](https://gitea.io/en-us/), MySQL, and [Traefik](https://containo.us/traefik/). This particular Gitea implementation uses a [Docker](https://en.wikipedia.org/wiki/Docker_(software)) Compose multi-container setup on Linux Ubuntu (`docker-compose.yml`). This work is just an extension of the intructions detailed at https://docs.gitea.io/en-us/install-with-docker/. This uses and creates the following:

* DNS domains hosted at [DigitalOcean](https://www.digitalocean.com/)
* TLS certificates using [LetsEncrypt](https://letsencrypt.org/), with DNS challenge
* Traefik API and Dashboard with basic HTTP auth login
* Gitea self-hosted git service
* MySQL database for Gitea

Also included is an alternate `docker-compose-base.yml`, which is an even more basic configuration that allows you to build other __Traefik__-fronted setups aside from Gitea. 

## Getting started
* Set up an Ubuntu 20.04 server, and install Docker and Docker Compose
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
* Create an empty `acme.json` file, and give it the required Traefik permissions (this file is unique to your setup and not kept in this repo):
```
touch acme.json
chmod 600 acme.json
```
* Next, create the external `web4` network:
```
docker network create --gateway 10.10.4.1 --subnet 10.10.4.0/24 web4
```
* Bring up the system:
```
docker-compose --verbose up -d
```

## Configuration
Once the system is running, go to your domain http://code.mydomain.com, click _Sign In_ and do the final system configuration. Also, the Traefik Dashboard should be available at http://traefik.mydomain.com. Both should switch from HTTP to HTTPS using SSL certs provided by LetsEncrypt.

## SSH
The web UI access is easy enough via the browser, but the more popular SSH access should also be available using user `git`, on your domain `code.mydomain.com`, and over port `22`. To simplify this access, it's easier to update your `$HOME/.ssh/config` file with a stanza such as this:
```
Host                     code.mydomain.com
  User                   git
  IdentityFile           /home/user/.ssh/id_ed25519
  StrictHostKeyChecking  no
  UserKnownHostsFile     /dev/null
```

## Networking
The `web4` network can of course be named anything you want, and so can the IP address. Just make sure all respective DNS names point to the right IPs.

## Backup and Restore
To backup a running system, run the `./luxbackup` script. The user context must have sudo privilege on the Docker host, in order to do a raw tar backup of the volumes under `/var/lib/docker/volumes/`.

Note, this is not a real-time backup process, and the service _will be stopped_ temporarily during this backup.

The resulting backup will produce a `luxbackup-dump.tgz` file made up of:
```
/var/lib/docker/volumes/lux_db/*
/var/lib/docker/volumes/lux_gitea/*
./.env
./acme.json
```

To restore a system, make sure you have a `luxbackup-dump.tgz` file, then run the `./luxrestore` script. Again, the user needs to have sudo privilege.

For a full recovery using an dump file, you will need to create the external `web4` network beforehand (see command above):

## References
1. https://docs.traefik.io/routing/routers/#certresolver
2. https://docs.gitea.io/en-us/install-with-docker/
3. https://www.howtoforge.com/tutorial/install-gitea-using-docker-on-ubuntu/
4. https://stackoverflow.com/questions/61774228/traefik-cant-connect-to-server-with-docker-compose
5. https://www.reddit.com/r/Traefik/comments/e9ubk2/le_wildcard_certificates_on_traefik_v2/
6. https://containo.us/blog/traefik-2-0-docker-101-fc2893944b9d/
7. https://www.smarthomebeginner.com/traefik-2-docker-tutorial/
