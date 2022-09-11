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

!!! warning
    For latest configs and compose files, visit my [Github](https://github.com/Hudater "Github Hudater"){:target="_blank" rel="noopener noreferrer"}

???+ Bare "Bare Metal"

    - [ ] Networking
        - [x] [Adguard](networking/adguard "DNS Server with Ad-blocking")
        - [ ] [Cloudflare Tunnel](networking/cloudflared/#bare-metal "Private tunnel to Cloudflare")
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
        - [x] [Cloudflare Tunnel](networking/cloudflared/#docker "Private tunnel to Cloudflare")
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

    - [ ] Installation Guides
        - [x] [Arch Linux](linux/arch/ "Arch installation guide")
        - [x] [Docker](linux/docker.md "Docker setup for Linux post-install")
    - [ ] Setup Guides
        - [x] [Basic](linux/basic.md "Basic setup for Linux post-install")
        - [x] [Desktop](linux/desktop.md "Setup for Desktop Linux")
        - [x] [Server](linux/server.md "Setup for linux servers")
    - [ ] Virtualization
        - [X] [Proxmox](linux/proxmox.md "Proxmox server")
