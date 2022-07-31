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

???+ Bare "Bare Metal"

    - [ ] Networking
        - [x] [Adguard](networking/adguard "DNS Server with Ad-blocking")
        - [x] [Cloudflare Tunnel](networking/cloudflared "Private tunnel to Cloudflare")
        - [x] [Shadowsocks](networking/shadowsocks "Tunnel Proxy")
    - [ ] Fileserver
        - [x] [Samba](filesv/samba "SMB File sharing")
        - [x] [Syncthing](filesv/syncthing/#bare-metal "File sync")
    - [ ] Management
        - [x] [OliveTin](management/olivetin "Command runner")

??? Docker "Docker - [CLICK TO EXPAND]"

    - [ ] Media
        - [x] [Bazarr](media/bazarr "Subtitles for Sonarr and Radarr")
        - [x] [Flaresolverr](media/flaresolverr "Bypass Cloudflare Protection")
        - [x] [Jellyfin](media/jellyfin "Media Server")
        - [x] [Jellyseerr](media/jellyseerr "Media Requester with GUI")
        - [x] [Prowlarr](media/prowlarr "Indexer")
        - [x] [qBittorrent](media/qbittorrent "Torrent Client")
        - [x] [Radarr](media/radarr "Movie Management")
        - [x] [Requestrr](media/requestrr.md "Media Request Chatbot")
        - [x] [Sonarr](media/sonarr "TV Show Management")
    - [ ] Networking
        - [x] [Authelia](networking/authelia "SSO Auth")
        - [x] [Cloudflare-DDNS](networking/cloudflare-ddns "DDNS for Cloudflare")
        - [x] [DuckDNS](networking/duckdns "Free DDNS")
        - [x] [Librespeed](networking/librespeed "FOSS Speedtest")
        - [x] [Rickroll](networking/rickroll "Yes")
        - [x] [Traefik](networking/traefik "Reverse Proxy")
        - [x] [WgEasy](networking/wgeasy "Wireguard server with GUI")
    - [ ] Monitoring
        - [x] [Dozzle](monitoring/dozzle "Docker Log manager")
        - [x] [Heimdall](monitoring/heimdall "GUI driven Dashboard")
        - [x] [Homarr](monitoring/homarr "*arr Integrated Dashboard")
        - [x] [Homer](monitoring/homer "Static Dashboard")
        - [x] [Scrutiny](monitoring/scrutiny "Disk monitoring")
        - [x] [Uptime Kuma](monitoring/uptime-kuma "Uptime Monitoring and Alert")
    - [ ] Fileserver
        - [x] [Filebrowser](filesv/filebrowser "Web based file browser")
        - [ ] [Syncthing](filesv/syncthing/#docker "File sync")
    - [ ] Management
        - [x] [Portainer-CE](management/portainer-ce "Docker Manager")
        - [x] [Portainer-Agent](management/portainer-agent "Docker Manager Endpoint")
        - [x] [Watchtower](management/watchtower "Update Docker Containers")

## Miscellaneous

!!! info

    This section links Linux docs, dotfiles etc

???+ Linux "Linux"

    - [x] [Basic Linux](linux/basic.md "Basic setup for Linux post-install")
    - [x] [Server](linux/server.md "Setup for linux servers")

## Templates

### Description

Give a basic description of the app here  

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `80`
    - [x] Other Ports: `443`
    - [x] Image: [DockerHub](https://hub.docker.com/r/hudater "PROJECT image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
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
#!/bin/sh
mkdir -p ${CFG_DIR}/containerCfgDir #(1)
mkdir -p ${CFG_DIR}/containerCfgDir/{multiple,subdirs} #(2)
docker compose up -d
```

1. Refer to [variables guide](/linux/server/#environment-variables)  
2. Use braces for variables and subdirectory names. No quotes  

### Template for link

- [DisplayName](LINK "HoverText"){:target="_blank" rel="noopener noreferrer"}
<!-- - []( ""){:target="_blank" rel="noopener noreferrer"} -->
