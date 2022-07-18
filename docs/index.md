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

    - [x] [Authelia](networking/authelia "SSO Auth")
    - [x] [Bazarr](media/bazarr "Subtitles for Sonarr and Radarr")
    - [x] [Filebrowser](filesv/filebrowser "Web based file browser")
    - [ ] [Syncthing](filesv/syncthing/#docker "File sync")
    - [x] [Traefik](networking/traefik "Reverse Proxy")

??? Bare "**Bare Metal** - [CLICK TO EXPAND]"

    - [x] [OliveTin](management/olivetin "Command runner")
    - [x] [Samba](filesv/samba "File sharing")
    - [x] [Syncthing](filesv/syncthing/#bare-metal "File sync")


## Miscellaneous

!!! info

    This section links Linux docs, dotfiles etc

???+ Linux "**Linux** - [CLICK TO EXPAND]"

    - [x] [Basic Linux](linux/basic.md "Basic setup for Linux post-install")
    - [x] [Server](linux/server.md "Setup for linux servers")

## Templates

### Description

Give a basic description of the app here  

### Basic info template

!!! base-info "Basic info with official links"

    - [x] Port: `80`
    - [x] Image: [DockerHub](https://hub.docker.com/u/hudater "PROJECT image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/Hudater/hudater.github.io "PROJECTNAME's Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [docs.HAops.dev](https://docs.haops.dev "PROJECTNAME's official website"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml title="Basic compose manifest"
version: "3"
services:
  serviceName:
    image: MAINTAINER/IMAGE:latest #(1)
    container_name: myContainer #(2)
    restart: unless-stopped
    volumes:
      - "${CFG_DIR}/containerCfgDir:/config" #(3)
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    ports:
      - 80:80 #(4)
    networks:
      - proxy #(5)
    labels:
      traefik.enable: true
      traefik.http.services.portainer.loadbalancer.server.port: portNumber #(6)

networks:
  proxy: #(7)
    external: true
```

1. No tag defaults to `latest`  
   Best practice is to test your stuff with new image and pin the container to that version
2. Name the container or docker would name it randomly  
   Name used by traefik to autogenerate route  
   i.e, `traefik.example.com`
3. Refer to [variables guide](/linux/server/#environment-variables)
4. Change port number on left side
5. Same custom docker network as traefik
6. Specific port for traefik to route traffic from for this container
7. Specify your custom networks location here

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/containerCfgDir #(1)
docker compose up -d
```

1. Refer to [variables guide](/linux/server/#environment-variables)  

### Template for link

- [DisplayName](LINK "HoverText"){:target="_blank" rel="noopener noreferrer"}
<!-- - []( ""){:target="_blank" rel="noopener noreferrer"} -->
