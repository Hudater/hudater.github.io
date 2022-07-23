---
tags:
  - Docker
  - Monitoring
title: Homarr
description: Dashboard with *arr integration
---
### Description

Customizable browser's home page with *arr integration

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `7575`
    - [x] Image: [Github Container Repo](https://ghcr.io/ajnart/homarr "Homarr image on Github"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/ajnart/homarr "Homarr Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Homarr Wiki](https://homarr.vercel.app/docs/about "Homarr official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: '3'
services:
  homarr:
    container_name: homarr
    image: ghcr.io/ajnart/homarr:latest
    restart: unless-stopped
    volumes:
      - "${BAK_CFG_DIR}/homarr:/app/data/configs"
    ports:
      - '7575:7575'
    networks:
      - proxy
    labels:
      traefik.enable: true

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir ${BAK_CFG_DIR}/homarr
docker compose up -d
```