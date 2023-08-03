---
title: Change to Docker-based system
date: 2023-08-03 08:15:24+08:00
categories: ["Server Maintainence","Service Changes"]
tags: []     # TAG names should always be lowercase
author: emoji
---
For easier management of services, Toyshost Servers are migrating to Docker-based system. The system is deployed onto TH2 and will be deployed onto TH3 on 2023-08-05 UTC+8.

## Concept

<img src="/assets/img/posts/2023-08-03-Change-to-Docker-based-system/concept.jpg" alt="Image describing the concept of Docker-based system" style="width:50%;margin-left: auto;margin-right: auto;"/>

HTTP connections going to Toyshost Servers are proxied through Cloudflare. The redirection of insecure connections to TLS-encrypted connections are done and enforced on the Cloudflare side. Then the encrypted connections are routed to the server, and eventually arrive the main NGINX Docker container by `-p 443:443` port forwarding rule.

Inside the server, a Docker network bridge is created to link up the main NGINX container and all other service containers. The main NGINX container uses this bridge to communicate with other web containers. Of course, non-web containers can also be hosted by forwarding other ports and bypassing Cloudflare.

All container configurations are stored on `/etc/docker-files/`. Inside each of the directories directories are the configuration files, persistence storage spaces and a `docker-compose.yaml` containing the necessary configurations needed to deploy the Docker containers.

## Advantages

1. **Easier to deploy.** A single `docker-compose.yaml` file handles everything of that service. The underlying code of Docker Compose handles everything related to image pulling, volume creation and service deployment. Upgrading the software to a newer version (or reverting it) is as simple as editing the compose file. With traditional methods, deploying a new service requires the creation of a user account, manual installation of all packages and scripts, and writing Systemd unit files.
2. **Portable.** In case the services have to be moved to another server, the relevant subdirectory of `/etc/docker-files/` can be copied to the equivalent on another server. Thanks to the portability of Docker images, the copied files should be able to run after taking care of file permissions and DNS records. However, with traditional methods, this involves dumping the SQL database from the server-wide MySQL/MariaDB, copying files and Systemd units from various places, ensure all the files are resting in their correct places on another server, and pray for it to work.

## Process of development

### Proof of concept (PoC)

<img src="/assets/img/posts/2023-08-03-Change-to-Docker-based-system/PoC-terminal-success.png" alt="Image describing the concept of Docker-based system" style="width:300px;float:right;"/>

The proof of concept (PoC) was done on a virtual machine (VM) running Arch Linux. On that VM, two services are deployed:

1. `main-http`: The NGINX proxy exposing port `80` (unencrypted HTTP)
2. `static-sites`: NGINX server serving static files. Proxied through `main-http` with the domain name `static-sites.local`.

The concept was considered feasible after the command `curl static-sites.local` returned a automaticly generated index of static files. The screenshot on the right showed the output, with part of its output trimmed.

<div style="clear:right;"></div>

### Deployment on TH2

The deployment to TH2 had done on 2023-07-29 UTC+8. The following services are deployed:

1. `main-http`: The NGINX proxy exposing port `443` (TLS-encrypted HTTP)
    * The download site ([`downloads-th2.1f616emo.xyz`](https://downloads-th2.1f616emo.xyz)), as a static site, is directly deployed inside `main-http`.
    * Different from the PoC server, that port `80` is not exposed. The redirection of insecure HTTP is done on Cloudflare side.
2. `http-admin`: NGINX-powered site serving admin-only tools. With PHP-FPM.
3. `http-filebrowser`: [filebrowser](https://github.com/filebrowser/filebrowser) instance.

### Planned deployment on TH3

The deployment to TH3 will be done on 2023-08-05 UTC+8. On that whole day, services may be down and should not be considered stable to use. The following servces will be deployed:

1. `main-http`: The NGINX proxy exposing port `443` (TLS-encrypted HTTP)
    * The TH3 download site (`downloads.1f616emo.xyz`), as a static site, will be directly deployed inside `main-http`.
    * This container will also handle the redirection from `*.thost3.de` to `*.1f616emo.xyz`.
    * Just like TH2, the `80` port is not exposed.
2. `http-dnotes`: [`dnotes`](https://github.com/Emojigit/dnotes) instance, note-taking website based on Django. Will be serving on `dnotes.1f616emo.xyz`.
3. `lilywhitebot-ts`: [LilyWhiteBot-typescript](https://github.com/sunafterrainwm/LilyWhiteBot-typescript) instance, forwarding messages between [Libera.chat](https://libera.chat), [Telegram](https://telegram.org) and [Discord](https://discord.com).
    * If webhooks are needed, they are proxied through `main-http`.
4. `mailserver`: [`docker-mailserver`](https://docker-mailserver.github.io/docker-mailserver/latest/) instance, for handling outgoing mails.
    * Mails coming to `1f616emo.xyz` are handled by Cloudflare.

The hardware upgrade of the server (from [VPS 200 G10s iv](https://www.netcup.eu/bestellen/produkt.php?produkt=2991) to [VPS 500 G10s iv](https://www.netcup.eu/bestellen/produkt.php?produkt=2992)) will also be done in the same maintenance window.
