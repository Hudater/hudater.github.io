---
tags:
  - Docker
  - Media
title: Prowlarr
description: Indexer manager/proxy for *arrs
---
### Description

Prowlarr is an indexer manager/proxy for *arrs, an alternative for Jackett

!!! warning
    This guide utilises Hardlinks  
    Refer to [Trash's Guide](https://trash-guides.info/Hardlinks/Hardlinks-and-Instant-Moves/ "Trash-Guides"){:target="_blank" rel="noopener noreferrer"} for elaborate description

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `9696`
    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/prowlarr "Prowlarr image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/Prowlarr/Prowlarr "Prowlarr Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Prowlarr Docs](https://wiki.servarr.com/prowlarr "Prowlarr official website"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.1"
services:
  prowlarr:
    image: linuxserver/prowlarr:develop
    container_name: prowlarr
    networks:
      - proxy # rename this to your custom docker network
    # network_mode: "container:gluetun"
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
      - DOCKER_MODS=ghcr.io/gilbn/theme.park:prowlarr
      - TP_THEME=organizr
    volumes:
      - "${CFG_DIR}/prowlarr:/config"
      - "${BAK_CFG_DIR}/prowlarr:/config/Backups"
    ports:
      - 9696:9696
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/prowlarr
mkdir -p "${BAK_CFG_DIR}"/prowlarr
docker compose up -d
```