---
tags:
  - Docker
  - Media
title: Requestrr
description: Chatbot for Media Requests
---
### Description

Requestrr is a chatbot used to simplify using services like Sonarr/Radarr/Ombi via the use of chat

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `4545`
    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/requestrr "Requestrr image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/darkalfx/requestrr "Requestrr Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Requestrr Docs](https://github.com/darkalfx/requestrr/wiki "Requestrr official website"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.1"
services:
  requestrr:
    image: linuxserver/requestrr:latest
    container_name: requestrr
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    volumes:
      - "${CFG_DIR}/requestrr:/config"
      - "${BAK_CFG_DIR}/requestrr/settings.json:/config/settings.json"
      - "${BAK_CFG_DIR}/requestrr/notifications.json:/config/notifications.json"
    ports:
      - 4545:4545
    networks:
      - proxy
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/requestrr
docker compose up -d
```