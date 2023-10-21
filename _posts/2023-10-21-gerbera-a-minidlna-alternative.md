---
title: "Gerbera - a minidlna alternative"
date: 2023-10-21
author: Ricardo Fuhrmann
layout: post
tags: minidlna, docker, gerbera
---

For some time I was using `minidlna` to serve some images that I have in my Raspberry PI, so I can see in my TV. Problem is that not every image loaded and I could not debug the problem. It simply didn't load some of the images. This made me search an alternative that was easy to set up as a docker container and easy to use.

Until I found [Gerbera](https://github.com/gerbera/gerbera), a "*UPnP media server which allows you to stream your digital media through your home network and consume it on a variety of UPnP compatible devices.*".

# First setup

To start, create a `docker-compose.yml ` file with the following:

```yaml
version: "3.3"
services:
  gerbera:
    image: gerbera/gerbera
    container_name: gerbera
    network_mode: host
    environment:
      PUID: 1000
      PGID: 1000
      TZ: America/Campo_Grande
    volumes:
      - gerbera-config:/var/run/gerbera
      - /mnt/dietpi_userdata/documentos/images:/content:ro

volumes:
  gerbera-config:
    external: false
```

After that just start the container and everything works!

```bash
$ docker-compose up -d && docker-compose logs -f
[+] Running 1/1
 ⠿ Container gerbera  Started                                                                                                 0.4s
gerbera  | 2023-07-05 07:41:51   info: Gerbera UPnP Server 1.12.1 - http://gerbera.io/
gerbera  | 2023-07-05 07:41:51   info: Copyright 2016-2022 Gerbera Contributors.
gerbera  | 2023-07-05 07:41:51   info: Gerbera is free software, covered by the GNU General Public License version 2
gerbera  | 2023-07-05 07:41:51   info: Loading configuration from: `/var/run/gerbera/config.xml`
gerbera  | 2023-07-05 07:41:51   info: Checking configuration...
gerbera  | 2023-07-05 07:41:51   info: Configuration check succeeded.
gerbera  | 2023-07-05 07:41:51   info: Loading 0 configuration items from database
gerbera  | 2023-07-05 07:41:51   info: Initialising UPnP with interface: <unset>, port: 49494
gerbera  | 2023-07-05 07:41:51   info: IPv4: Server bound to: 192.168.1.50:49494
gerbera  | 2023-07-05 07:41:51   info: IPv6: Server bound to: :0
gerbera  | 2023-07-05 07:41:51   info: IPv6 ULA/GLA: Server bound to: :0
gerbera  | 2023-07-05 07:41:51   info: Will send UPnP Alive advertisements every 60 seconds
gerbera  | 2023-07-05 07:41:51   info: The Web UI can be reached by following this link: http://192.168.1.50:49494
```
![1.png](/assets/images/gerbera-a-minidlna-alternative/1.jpg)

![2.png](/assets/images/gerbera-a-minidlna-alternative/2.jpg)

You can access the dashboard at `http://IP:49494` (update with your IP) to check the connections, files and more:

![3.png](/assets/images/gerbera-a-minidlna-alternative/3.png)

# Easy configuration

You can check out the Gerbera configuration overview [here](https://docs.gerbera.io/en/stable/config-overview.html), there are a lot of options to configure. But to start you can get a template config from gerbera itself by running the `--create-config` command:

```bash
docker-compose exec gerbera gerbera --create-config
```

It'll output to std the configuration template. Or even better, you can configure directly from the dashboard:

![4.png](/assets/images/gerbera-a-minidlna-alternative/4.png)

