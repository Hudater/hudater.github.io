---
tags:
  - Docker
  - Media
title: Jellyfin
description: Media Server
---
### Description

Jellyfin is an open-source media server alternative to Plex

!!! warning
    This guide utilises Hardlinks  
    Refer to [Trash's Guide](https://trash-guides.info/Hardlinks/Hardlinks-and-Instant-Moves/ "Trash-Guides"){:target="_blank" rel="noopener noreferrer"} for elaborate description

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `8096`
    - [x] Other Ports: `7359` `1900`
    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/jellyfin "Jellyfin image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/jellyfin/jellyfin "Jellyfin Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Jellyfin Docs](https://jellyfin.org/docs/ "Jellyfin official website"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.1"
services:
  jellyfin:
    image: linuxserver/jellyfin:latest
    container_name: jellyfin
    networks:
      - proxy # rename this to your custom docker network
    group_add:
      - 109 #adding group `render` so that VAAPI can use the almighrty radeon r5
      #remeber to change the group id which is present in /etc/group
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
      - JELLYFIN_PublishedServerUrl=192.168.29.11 #optional
    volumes:
      - "${CFG_DIR}/jellyfin:/config"
      - "${DATA_DIR}:/data"
    ports:
      - 8096:8096
      - 7359:7359/udp #optional
      - 1900:1900/udp #optional
    devices:
      - /dev/dri/renderD128:/dev/dri/renderD128 #optional
      - /dev/dri/card0:/dev/dri/card0
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/jellyfin
docker compose up -d
```