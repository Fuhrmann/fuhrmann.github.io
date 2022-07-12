---
title: "Setting up minidlna as a docker container"
date: 2022-07-11
author: Ricardo Fuhrmann
layout: post
permalink: /setting-up-minidlna-as-docker-container/
tags: software, linux
---

Minidlna is a server software fully compliant with DLNA/UPnpP clientes. It means it'll server files to clients over network, for example, your TV. Here's a quick setup for a `docker-compose.yml` plug and play into your server:
``` yaml
version: '3'

services:
  dlna:
    image: vladgh/minidlna
    container_name: minidlna
    restart: always
    volumes:
      - /mnt/movies:/media/movies
    network_mode: host
    environment:
      MINIDLNA_MEDIA_DIR: /media
      MINIDLNA_FRIENDLY_NAME: pc
```
> Notice that we're using a custom image from [vladgh](https://hub.docker.com/r/vladgh/minidlna), but feel free to choose any other image as you like (there's quite a [few](https://hub.docker.com/search?q=minidlna!). In this case I'm using one which offers support for `arm` since I'll be setting up this into my raspberry pi.

In case you want additional folders, you can setup like this:
``` yaml
    volumes:
      - /mnt/audio:/media/audio
      - /mnt/video:/media/video
    environment:
      MINIDLNA_MEDIA_DIR_1: /media/audio
      MINIDLNA_MEDIA_DIR_2: /media/video
      MINIDLNA_FRIENDLY_NAME: pc
```

After that start your container as usual:

```shell
user@raspberrypi [~/apps/minidlna]
$ docker-compose up -d && docker-compose logs -f
[+] Running 1/1
 ⠿ Container minidlna  Started
minidlna  | === Set user and group identifier
minidlna  | === Set standard configuration
minidlna  | === Set configuration from environment variables
minidlna  | === Set permissions
minidlna  | === Generate scan/rebuild flags
minidlna  | === Start daemon
minidlna  | minidlna.c:1124: warn: Starting MiniDLNA version 1.3.0.
minidlna  | minidlna.c:395: warn: Creating new database at /minidlna/cache/files.db
minidlna  | minidlna.c:1193: warn: HTTP listening on port 8200
minidlna  | scanner.c:731: warn: Scanning /media
minidlna  | scanner.c:820: warn: Scanning /media finished (287 files)!
```

Checkout minidlna's [documentation](https://manpages.ubuntu.com/manpages/kinetic/en/man5/minidlna.conf.5.html) for reference.
