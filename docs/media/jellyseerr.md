---
tags:
  - Docker
  - Media
title: Jellyseerr
description: Media requester for Jellyfin and Emby
---
### Description

Media requester for Jellyfin and Emby. An overseerr alternative for Jellyfin and Emby users

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `5055`
    - [x] Image: [DockerHub](https://hub.docker.com/r/fallenbagel/jellyseerr "Jellyseerr image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/fallenbagel/jellyseerr "Jellyseerr Github repo"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: '3'
services:
  jellyseerr:
    image: fallenbagel/jellyseerr:latest
    container_name: jellyseerr
    environment:
      - LOG_LEVEL=debug
      - TZ=${TZ}
    volumes:
      - "${CFG_DIR}/jellyseerr:/app/config"
      - "${BAK_CFG_DIR}/jellyseerr/settings.json:/app/config/settings.json"
    ports:
      - 5055:5055
    networks:
      - proxy
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/jellyseerr
docker compose up -d
```