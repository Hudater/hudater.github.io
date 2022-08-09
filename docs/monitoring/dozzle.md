---
tags:
  - Docker
  - Monitoring
title: Dozzle
description: Realtime log viewer for docker containers
---
### Description

Realtime log viewer for docker containers meant for debugging

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `9999`
    - [x] Image: [DockerHub](https://hub.docker.com/r/amir20/dozzle "Dozzle image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/amir20/dozzle "Dozzle Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Dozzle Wiki](https://github.com/amir20/dozzle#environment-variables-and-configuration "Dozzle official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "3"
services:
  dozzle:
    container_name: dozzle
    image: amir20/dozzle:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - 9999:8080
    labels:
      traefik.enable: true
    networks:
      - proxy
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
docker compose up -d
```