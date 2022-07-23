---
tags:
  - Docker
  - Networking
title: Rickroll
description: Rickroll
---
### Description

Self explanatory

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `6519`
    - [x] Image: [DockerHub](https://hub.docker.com/r/modem7/docker-rickroll "Image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/modem7/docker-rickroll "Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Wiki](https://www.youtube.com/watch?v=dQw4w9WgXcQ "Official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.4"
services:
  rickroll:
    image: modem7/docker-rickroll:onclick
    container_name: rickroll
    networks:
      - proxy # rename this to your custom docker network
    labels:
      traefik.enable: true
    ports:
      - 6519:8080
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
docker compose up -d
```