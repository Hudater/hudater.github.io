---
tags:
  - Docker
  - Media
title: qBittorrent
description: BitTorrent Client
---
### Description

qBittorrent is an open-source BitTorrent Client  
This image comes with vue.js theme

!!! warning
    This guide utilises Hardlinks  
    Refer to [Trash's Guide](https://trash-guides.info/Hardlinks/Hardlinks-and-Instant-Moves/ "Trash-Guides"){:target="_blank" rel="noopener noreferrer"} for elaborate description

!!! note
    For extended guide, refer to [TRaSH-Guides](https://trash-guides.info/Downloaders/qBittorrent/Basic-Setup/ "Trash-Guides"){:target="_blank" rel="noopener noreferrer"}

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `4000`
    - [x] Image: [DockerHub](https://hub.docker.com/r/hotio/qbittorrent "qBittorrent image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/qbittorrent/qBittorrent "qBittorrent Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [qBittorrent Docs](https://github.com/qbittorrent/qBittorrent/wiki "qBittorrent official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "3.7"
services:
  qbittorrent:
    container_name: qbit
    image: hotio/qbittorrent:latest
    networks:
      - proxy # rename this to your custom docker network
    ports:
      - "4000:8080"
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - UMASK=002
      - TZ=${TZ}
    volumes:
      - "${CFG_DIR}/qbittorrent:/config"
      - "${DATA_DIR}/Torrents:/data/Torrents"
      - "${DATA_DIR}/Del:/Del"
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/qbittorrent
docker compose up -d
```

## Settings
```bash title="Path settings line-by-line"
/data/Torrents/complete
/data/Torrents/downloading
/data/Torrents/torfiles/bak/added
/data/Torrents/torfiles/bak/complete
/data/Torrents/torfiles
```

<!-- TODO: Add image here -->