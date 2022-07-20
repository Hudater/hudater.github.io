---
tags:
  - Docker
  - Management
title: Portainer-Agent
description: Agent for portainer-ce to connect to
---
### Description

Portainer-agent provides an endpoint for Portainer-CE to connect to and manage your docker instance via GUI

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Port: `9001`
    - [x] Image: [DockerHub](https://hub.docker.com/r/portainer/agent "Official Image"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/portainer/agent "Official Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Portainer-agent Docs](https://docs.portainer.io/start/install/agent/docker/linux "Portainer-agent Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "2.1"
services:
  portainer-agent:
    image: portainer/agent:latest
    container_name: port-agent
    networks:
      - proxy # rename this to your custom docker network 
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    ports:
      - 9001:9001
    restart: unless-stopped
    volumes:
      - /var/lib/docker/volumes:/var/lib/docker/volumes
      - /var/run/docker.sock:/var/run/docker.sock

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
docker compose up -d
```