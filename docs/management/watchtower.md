---
tags:
  - Docker
  - Management
title: Watchtower
description: Watchtower auto-updates your docker containers
---
### Description

Watchtower can auto-updates your docker containers, pull latest image, notify you of new image or updated container and some more

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Image: [DockerHub](https://hub.docker.com/r/containrrr/watchtower "Official image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/containrrr/watchtower/ "Official Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Docs](https://containrrr.dev/watchtower/ "Official documentation"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "3"
services:
  watchtower:
    image: containrrr/watchtower:latest
    container_name: watchtower
    networks:
      - proxy # rename this to your custom docker network
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /etc/localtime:/etc/localtime:ro
    environment:
      WATCHTOWER_CLEANUP: "true"
      WATCHTOWER_INCLUDE_RESTARTING: "true"
      WATCHTOWER_ROLLING_RESTART: "true"
      WATCHTOWER_SCHEDULE: 0 0 3 * * * #at 3 AM everyday
      WATCHTOWER_NOTIFICATION_URL: ${WATCHTOWER_URL}
      WATCHTOWER_DEBUG: "true"
      WATCHTOWER_POLL_INTERVAL: "86400"
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
docker compose up -d
```