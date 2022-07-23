---
tags:
  - Docker
  - Networking
title: DuckDNS
description: Free DDNS Service
---
### Description

DuckDNS is free dynamic DNS service hosted on AWS. It provides 5 subdomains for free

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/duckdns "DuckDNS image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/linuxserver/docker-duckdns "DuckDNS Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Linuxserver Wiki](https://docs.linuxserver.io/images/docker-duckdns "DuckDNS official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.1"
services:
  duckdns:
    image: linuxserver/duckdns:arm32v6-latest #using this on pi zero so armv6 is tagged
    container_name: duckdns
    networks:
      - proxy # rename this to your custom docker network
    environment:
      - PUID=${PUID} #optional
      - PGID=${PGID} #optional
      - TZ=${TZ}
      - SUBDOMAINS="${DD_PRI}","${DD_SEC}"
      - TOKEN="${DD_TOKEN}"
      - LOG_FILE=true #optional
    volumes:
      - "${CFG_DIR}/duckdns:/config" #optional
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/duckdns
docker compose up -d
```