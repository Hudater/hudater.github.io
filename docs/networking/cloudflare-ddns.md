---
tags:
  - Docker
  - Networking
title: Cloudflare-DDNS
description: Cloudflare-DDNS
---
### Description

This small Alpine Linux based Docker image by Oznu will allow you to use the free CloudFlare DNS Service as a Dynamic DNS Provider (DDNS).

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Image: [DockerHub](https://hub.docker.com/r/oznu/cloudflare-ddns "Cloudflare-DDNS image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/oznu/docker-cloudflare-ddns "Cloudflare-DDNS Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Cloudflare-DDNS Wiki](https://github.com/oznu/docker-cloudflare-ddns#parameters "Cloudflare-DDNS official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: '2'
services:
  cloudflare-ddns:
    image: oznu/cloudflare-ddns:latest
    container_name: cflare-ddns
    networks:
      - proxy # rename this to your custom docker network
    restart: always
    environment:
      - API_KEY=$CF_DDNS_API
      - ZONE=$CF_CLOUD_DOMAIN
      - SUBDOMAIN=$CF_DDNS_HOME
      - PROXIED=false

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
docker compose up -d
```