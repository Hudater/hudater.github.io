---
hide:
  - navigation
  - toc
tags:
  - Home
  - Docs
title: Homepage
description: Homepage of this glorious website
---
# Welcome to my personal documentation

[![GitHub last commit](https://img.shields.io/github/last-commit/Hudater/hudater.github.io/main?color=4051B5&style=for-the-badge)](https://github.com/Hudater/hudater.github.io/commits/main){:target="_blank" rel="noopener noreferrer"}
[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/Hudater/hudater.github.io/ci?color=4051B5&style=for-the-badge)](https://github.com/Hudater/hudater.github.io/actions){:target="_blank" rel="noopener noreferrer"}
[![Pages status](https://img.shields.io/website?color=4051B5&style=for-the-badge&url=https%3A%2F%2Fdocs.haops.dev%2F)](https://docs.haops.dev/){:target="_blank" rel="noopener noreferrer"}

Centralized documentation for my Homelab, OS setup and possibly more  
This website grows per my convenience. I hope it may be useful to you too!  


## Service Listing

!!! info

    This section links services I use in my lab  
    Ticked options are the one deployed all the times  

???+ Docker "**Docker** - [CLICK TO EXPAND]"

    - [x] [Authelia](networking/authelia.md "SSO Auth")
    - [x] [Bazarr](media/bazarr.md "Subtitles for Sonarr and Radarr")
    - [x] [Traefik](networking/traefik.md "Reverse Proxy")

??? Bare "**Bare Metal** - [CLICK TO EXPAND]"

    - [x] Lorem ipsum dolor sit amet, consectetur adipiscing elit
    - [ ] Vestibulum convallis sit amet nisi a tincidunt
        * [x] In hac habitasse platea dictumst
        * [x] In scelerisque nibh non dolor mollis congue sed et metus
        * [ ] Praesent sed risus massa
    - [ ] Aenean pretium efficitur erat, donec pharetra, ligula non scelerisque


## Miscellaneous

!!! info

    This section links Linux docs, dotfiles etc

???+ Linux "**Linux** - [CLICK TO EXPAND]"

    - [x] [Basic Linux](linux/basic.md "Basic setup for Linux post-install")

## Templates

#### Basic info template

!!! base-info "Basic info with official links"

    - [x] Port: `443`
    - [x] Image: [DockerHub](https://hub.docker.com/u/hudater "PROJECT image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/Hudater/hudater.github.io "PROJECTNAME's Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [HAops.dev](https://docs.haops.dev "PROJECTNAME's official website"){:target="_blank" rel="noopener noreferrer"}

#### Compose file template

```yaml
version: "3"
services:
  serviceName: #change me
    image: MAINTAINER/IMAGE:latest #change me
    container_name: myContainer #change me
    restart: unless-stopped
    volumes:
      - "${CFG_DIR}/containerCfgDir:/config" #change me
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    ports:
      - 80:80
    labels:
      traefik.enable: true
      traefik.http.services.portainer.loadbalancer.server.port: portNumber #set specific port for traefik
    networks:
      - proxy

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

#### Deploy.sh file

```bash
mkdir -p "${CFG_DIR}"/dirName #change me
docker compose up -d
```

#### Template for link

- [DisplayName](LINK "HoverText"){:target="_blank" rel="noopener noreferrer"}
<!-- - []( ""){:target="_blank" rel="noopener noreferrer"} -->