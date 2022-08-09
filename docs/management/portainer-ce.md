---
tags:
  - Docker
  - Management
title: Portainer-CE
description: Portainer Community Edition
---
### Description

Portainer provides an intuitive GUI to manage your docker instance

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `9000`
    - [x] Other Ports: `8000`
    - [x] Image: [DockerHub](https://hub.docker.com/r/portainer/portainer "Portainer-CE image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/portainer/portainer "Portainer-CE Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Portainer-CE Docs](https://docs.portainer.io/start/install/server/docker/linux "Portainer official website"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: '3'
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    networks:
      - proxy # rename this to your custom docker network
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    ports:
      - 8000:8000
      - 9000:9000
    volumes:
      - portainer_data:/data
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
    labels:
      traefik.enable: true
      traefik.http.services.portainer.loadbalancer.server.port: 9000

volumes:
  portainer_data:

networks:
  proxy:
    external: true
```

### deploy.sh

```bash
docker compose up -d
```