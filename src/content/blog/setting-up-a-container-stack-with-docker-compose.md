---
title: "Setting up a Docker container stack with Docker Compose"
description: "I've been running a bunch of services on my home server in docker containers for a few years now. It's quick and easy to set up once you get used to it. Here's a quick and dirty guide to installing Docker and Docker Compose, and getting several containers up and running."
pubDate: "September 29, 2022"
updatedDate: "August 11, 2023"
tags:
  - Self-Hosting
  - Docker
  - Linux
---

## Sections

1. [Installing Docker and Docker-Compose](#install)
2. [Setting up a Docker-Compose file](#compose)
3. [Starting up the containers](#start)
4. [References](#ref)

> **ⓘ Note**<br><br> This will not be an exhaustive Docker tutorial, for that I always suggest <a href="https://docs.docker.com" target="_blank">the official Docker documentation</a>. The purpose of this guide is limited to explaining how to install Docker and set up various Docker containers quickly and easily via Docker-Compose.

<div id='install'/>

## Installing Docker and Docker Compose

I'll be quoting from the <a href="https://docs.docker.com/engine/install" target="_blank">Docker docs</a>, which as of mid-2023 recommend uninstalling older packages like the separate `docker-compose` in favor of the new `docker-compose-plugin`, among other things.

This post assumes you are installing on **Debian** or **Ubuntu**, check the Docker docs for installation instructions on CentOS and it's offshoots, Fedora, RHEL, or Raspbian.

First, uninstall all conflicting packages:

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt remove $pkg; done
```

Install necessary packages to allow `apt` to use a repository over HTTPS:

```bash
sudo apt update
sudo apt install ca-certificates curl gnupg
```

Add Docker's official GPG key:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

Set up the repository:

```bash
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Now install Docker:

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo service docker start && sudo service docker enable
```

<div id='compose'/>

## Setting up a Docker Compose file

For setting up multiple containers via CLI we'll be using <a href="https://docs.docker.com/compose/" target="_blank">Docker Compose</a>, which uses YAML files for configuration. Below is a `docker-compose.yml` based on own:

```yaml
version: "3"

services:
  plex:
    container_name: plex
    restart: unless-stopped
    image: linuxserver/plex:latest
    network_mode: host
    volumes:
      - ~/docker/plex:/config
      - ~/media/music:/music
      - ~/media/photos:/photos
      - ~/media/movies:/movies
      - ~/media/tvshows:/tvshows
      - ~/media/movies:/4kmovies
      - ~/media/tvshows:/4ktvshows
    environment:
      - TZ=America/New_York
      - PLEX_UID=1000
      - PLEX_GID=1000

  airsonic-advanced:
    image: lscr.io/linuxserver/airsonic-advanced:latest
    container_name: airsonic
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
    volumes:
      - ~/docker/airsonic:/config
      - ~/media/music:/music
      - ~/media/music/playlists:/playlists
      - ~/media/music/podcasts:/podcasts
    ports:
      - 4040:4040
    restart: unless-stopped

  ubooquity:
    image: linuxserver/ubooquity
    container_name: ubooquity
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
    volumes:
      - ~/docker/ubooquity:/config
      - ~/media/ebooks/books:/books
      - ~/media/ebooks/comics:/comics
    restart: unless-stopped
    ports:
      - 2202:2202
      - 2203:2203

  filebrowser:
    container_name: filebrowser
    restart: unless-stopped
    image: filebrowser/filebrowser:latest
    volumes:
      - ~/:/srv
      - ~/docker/filebrowser/filebrowser.db:/database/filebrowser.db
      - ~/docker/filebrowser/settings.json:/config/settings.json
    ports:
      - 8080:80
    environment:
      - TZ=America/New_York
      - PUID=1000
      - PGID=1000

  qflood:
    container_name: qflood
    image: cr.hotio.dev/hotio/qflood
    ports:
      - 8800:8080
      - 3300:3000
    environment:
      - PUID=1000
      - PGID=1000
      - UMASK=002
      - TZ=America/New_York
      - FLOOD_AUTH=false
    volumes:
      - ~/docker/qflood:/config
      - ~/media/downloads:/downloads
      - ~/media/movies:/4kmovies
      - ~/media/tvshows:/4ktvshows

  watchtower:
    image: containrrr/watchtower
    container_name: watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    restart: always
```

This will create and run the following containers:

- **Plex**: Personal media streaming server
- **Airsonic**: Music player and streaming server
- **Ubooquity**: Read and manage ebooks and comics
- **File Browser**: A web-based file manager
- **qFlood**: qBittorrent and Flood UI
- **Watchtower**: Automatically updates containers

<div id='start'/>

## Starting up the containers

Once the `docker-compose.yml` is ready, use the following command in the same directory where the file is located:

```bash
docker compose up -d
```

Using the `-d` flag will "detach" the process so it's in the background. You will see all the required container images be downloaded and the directories you specified in the compose file will also be set up. Once finished, use `docker ps` to list running containers and confirm your new services are up and running.

<div id='ref'/>

## References

- <a href="https://docs.docker.com" target="_blank">Docker documentation</a>
- <a href="https://docs.docker.com/compose/" target="_blank">Docker Compose documentation</a>
