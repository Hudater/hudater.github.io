---
tags:
  - Docker
  - Monitoring
title: Heimdall
description: Simple Dashboard
---
### Description

Heimdall is a way to organise all those links to your most used web sites and web applications in a simple way

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `82`
    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/heimdall "Heimdall image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/linuxserver/Heimdall "Heimdall Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Heimdall Wiki](https://docs.linuxserver.io/images/docker-heimdall "Heimdall official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.1"
services:
  heimdall:
    image: linuxserver/heimdall:latest
    container_name: heimdall
    networks:
      - proxy # rename this to your custom docker network
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    volumes:
      - "${CFG_DIR}/heimdall:/config"
    ports:
      - 82:80
    labels:
      traefik.enable: true
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/heimdall
docker-compose up -d

# increase the max upload size in php-local.ini file:
# the command below invokes a bash shell and then echoes the line to the end of the file)
echo "Sleeping 5 seconds"
sleep 5
docker exec -it heimdall bash -c "echo upload_max_filesize = 30M >> /config/php/php-local.ini"
docker restart heimdall
```