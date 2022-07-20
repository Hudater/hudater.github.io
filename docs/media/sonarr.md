---
tags:
  - Docker
  - Media
title: Sonarr
description: TV Show Manager
---
### Description

Sonarr is a TV Show collection manager for Usenet and BitTorrent users  
It basically automatically downloads, renames and manages your TV Shows while respecting your custom settings

!!! warning
    This guide utilises Hardlinks  
    Refer to [Trash's Guide](https://trash-guides.info/Hardlinks/Hardlinks-and-Instant-Moves/ "Trash-Guides"){:target="_blank" rel="noopener noreferrer"} for elaborate description

!!! note
    For extended guide, refer to [TRaSH-Guides](https://trash-guides.info/Sonarr/ "Trash-Guides"){:target="_blank" rel="noopener noreferrer"}

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `8989`
    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/sonarr "Sonarr image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/Sonarr/Sonarr "Sonarr Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Sonarr Docs](https://wiki.servarr.com/sonarr "Sonarr official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.1"
services:
  sonarr:
    image: linuxserver/sonarr:latest
    container_name: sonarr
    networks:
      - proxy # rename this to your custom docker network
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
      - DOCKER_MODS=ghcr.io/gilbn/theme.park:sonarr
      - TP_THEME=organizr
    volumes:
      - ${CFG_DIR}/sonarr:/config
      - ${BAK_CFG_DIR}/sonarr:/config/Backups
      - ${DATA_DIR}:/data
    ports:
      - 8989:8989
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/sonarr
mkdir -p "${BAK_CFG_DIR}"/sonarr
docker compose up -d
```