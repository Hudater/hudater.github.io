---
tags:
  - Docker
  - Media
title: Bazarr
description: Subtitle downloader
---
### Description

Bazarr automatically downloads subtitles for your Movies and TV Shows by integrating with Sonarr

!!! warning
    This guide utilises Hardlinks  
    Refer to [Trash's Guide](https://trash-guides.info/Hardlinks/Hardlinks-and-Instant-Moves/ "Trash-Guides"){:target="_blank" rel="noopener noreferrer"} for elaborate description

!!! note
    For extended guide, refer to [TRaSH-Guides](https://trash-guides.info/Bazarr/ "Trash-Guides"){:target="_blank" rel="noopener noreferrer"}

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `6767`
    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/bazarr "Bazarr image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/morpheus65535/bazarr "Bazarr Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Wiki](https://wiki.bazarr.media/ "Bazarr official website"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.1"
services:
  bazarr:
    image: linuxserver/bazarr:latest
    container_name: bazarr
    networks:
      - proxy # rename this to your custom docker network
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
      - DOCKER_MODS=ghcr.io/gilbn/theme.park:bazarr
      - TP_THEME=organizr
    volumes:
      - ${CFG_DIR}/bazarr:/config
      - ${BAK_CFG_DIR}/bazarr:/config/Backups
      - ${DATA_DIR}/Media:/data/Media
    ports:
      - 6767:6767
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/bazarr
mkdir -p "${BAK_CFG_DIR}"/bazarr
docker compose up -d
```