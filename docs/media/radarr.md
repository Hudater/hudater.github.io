---
tags:
  - Docker
  - Media
title: Radarr
description: Movie Collection Manager
---
### Description

Radarr is a movie collection manager for Usenet and BitTorrent users  
It basically automatically downloads, renames and manages your movies while respecting your custom settings

!!! warning
    This guide utilises Hardlinks  
    Refer to [Trash's Guide](https://trash-guides.info/Hardlinks/Hardlinks-and-Instant-Moves/ "Trash-Guides"){:target="_blank" rel="noopener noreferrer"} for elaborate description

!!! note
    For extended guide, refer to [TRaSH-Guides](https://trash-guides.info/Radarr/ "Trash-Guides"){:target="_blank" rel="noopener noreferrer"}

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `7878`
    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/radarr "Radarr image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/Radarr/Radarr "Radarr Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Radarr Docs](https://wiki.servarr.com/radarr "Radarr official Wiki"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.1"
services:
  radarr:
    image: linuxserver/radarr:latest
    container_name: radarr
    networks:
      - proxy # rename this to your custom docker network
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
      - DOCKER_MODS=ghcr.io/gilbn/theme.park:radarr
      - TP_THEME=organizr
    volumes:
      - ${CFG_DIR}/radarr:/config
      - ${BAK_CFG_DIR}/radarr:/config/Backups
      - ${DATA_DIR}:/data
    ports:
      - 7878:7878
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/radarr
mkdir -p "${BAK_CFG_DIR}"/radarr
docker compose up -d
```