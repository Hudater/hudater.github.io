---
tags:
  - Docker
  - Networking
title: Librespeed
description: Opensource Speedtest
---
### Description

LibreSpeed is a Free and Open Source speedtest that you can host on your server(s), and users can run in their browser.

!!! note
    Follow this [link](https://github.com/librespeed/speedtest/wiki/Making-a-custom-front-end){:target="_blank" rel="noopener noreferrer"} to change the Front-end UI
## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `3006`
    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/librespeed "Librespeed image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/librespeed/speedtest "Librespeed Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Librespeed Wiki](https://github.com/librespeed/speedtest/wiki "Librespeed official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "3.1"
services:
  librespeed:
    image: linuxserver/librespeed:latest
    container_name: librespeed
    networks:
      - proxy
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
      - DOCKER_MODS=ghcr.io/gilbn/theme.park:librespeed
      - TP_THEME=dark
      # - PASSWORD=PASSWORD
      # - CUSTOM_RESULTS=false #optional
      # - DB_TYPE=sqlite #optional
      # - DB_NAME=DB_NAME #optional
      # - DB_HOSTNAME=DB_HOSTNAME #optional
      # - DB_USERNAME=DB_USERNAME #optional
      # - DB_PASSWORD=DB_PASSWORD #optional
      # - DB_PORT=DB_PORT #optional
    volumes:
      - "${CFG_DIR}/librespeed:/config"
    ports:
      - 3006:80
    labels:
      traefik.enable: true
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${BAK_CFG_DIR}"/librespeed
docker compose up -d
```