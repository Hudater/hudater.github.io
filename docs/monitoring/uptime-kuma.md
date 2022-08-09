---
tags:
  - Docker
  - Monitoring
title: Uptime Kuma
description: Uptime Monitoring and Alerts
---
### Description

A fancy self-hosted monitoring tool with various services for alerts

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `3001`
    - [x] Image: [DockerHub](https://hub.docker.com/r/louislam/uptime-kuma "Uptime-Kuma image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/louislam/uptime-kuma "Uptime-Kuma Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Uptime-Kuma Wiki](https://github.com/louislam/uptime-kuma/wiki "Uptime-Kuma official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: '3.3'
services:
  uptime:
    image: louislam/uptime-kuma:latest
    container_name: uptime
    networks:
      - proxy # rename this to your custom docker network
    labels:
      traefik.enable: true
    volumes:
      - "${CFG_DIR}/uptime:/app/data"
    ports:
      - 3001:3001
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/uptime
docker compose up -d
```