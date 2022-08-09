---
tags:
  - Docker
  - Media
title: Flaresolverr
description: Bypass Cloudflare and Captcha
---
### Description

FlareSolverr is a proxy server to bypass Cloudflare protection

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Port: `8191`
    - [x] Image: [DockerHub](https://hub.docker.com/r/flaresolverr/flaresolverr "Flaresolverr on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/FlareSolverr/FlareSolverr "Flaresolverr Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Flaresolverr Docs on Servarr](https://wiki.servarr.com/en/prowlarr/settings "Flaresolverr Docs on Servarr"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.1"
services:
  flaresolverr:
    image: flaresolverr/flaresolverr:latest
    container_name: flaresolverr
    networks:
      - proxy # rename this to your custom docker network
    environment:
      - LOG_LEVEL=${LOG_LEVEL:-info}
      - LOG_HTML=${LOG_HTML:-false}
      - CAPTCHA_SOLVER=${CAPTCHA_SOLVER:-none}
      - TZ=${TZ}
    ports:
      - "${PORT:-8191}:8191"
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
docker compose up -d
```