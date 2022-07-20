---
tags:
  - Bare Metal
  - Docker
  - Fileserver
  - Files
  - Sync
title: Syncthing
description: File syncing for multiple devices
---
### Description

Syncthing is a continuous file synchronization program. Synchronizes files between two or more computers in real time

!!! note

    This service is deployed on multiple machines  
    I personally use bare metal installation

## Bare Metal

### Basic info

!!! base-info "Basic info with official links"

    - [x] Package: `syncthing`
    - [x] Official Wiki: [Official Syncthing Wiki](https://docs.syncthing.net "Official Syncthing Wiki"){:target="_blank" rel="noopener noreferrer"}
    - [x] Arch Wiki: [Arch Wiki for Syncthing](https://wiki.archlinux.org/title/Syncthing "Arch Wiki for Syncthing"){:target="_blank" rel="noopener noreferrer"}

### Getting started

- [x] Install Syncthing package

    === "Arch"
        ```bash
        sudo pacman -Sy syncthing
        ```

    === "Debian/Ubuntu"
        [Official Syncthing Guide](https://apt.syncthing.net/ "Official Syncthing Guide"){:target="_blank" rel="noopener noreferrer" .md-button .md-button--primary}

- [x] Allow Web-GUI to be accesible to all addresses:
    ```bash
    sed -i 's/127.0.0.1:8384/0.0.0.0:8384/' .config/syncthing/config.xml
    ```

- [x] Allow syncthing through firewall

    === "UFW"
        [Syncthing's UFW Guide](https://docs.syncthing.net/users/firewall.html#uncomplicated-firewall-ufw "Official Syncthing Guide"){:target="_blank" rel="noopener noreferrer" .md-button .md-button--primary}

    === "Firewalld"
        [Syncthing's Firewalld Guide](https://docs.syncthing.net/users/firewall.html#firewalld "Official Syncthing Guide"){:target="_blank" rel="noopener noreferrer" .md-button .md-button--primary}

!!! warning
    If you have selinux or apparmor enabled, create a policy allowing syncthing

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `8384`
    - [x] Other Ports: `22000` `21027`
    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/syncthing "Syncthing image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/syncthing/syncthing "Syncthing's Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Syncthing.net](https://syncthing.net/ "Syncthing's official website"){:target="_blank" rel="noopener noreferrer"}

### docker compose.yml

```yaml
---
version: "3.1"
services:
  syncthing:
    image: linuxserver/syncthing:latest
    container_name: sync
    hostname: syncthing #optional
    networks:
      - proxy # rename this to your custom docker network
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    volumes:
      - "${CFG_DIR}/syncthing:/config"
      - /home/$USER:/home_dir
      - /:/root_dir
    labels:
      traefik.enable: true
      traefik.http.services.portainer.loadbalancer.server.port: 8384
    ports:
      - 8384:8384
      - 22000:22000/tcp
      - 22000:22000/udp
      - 21027:21027/udp
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${CFG_DIR}"/syncthing
docker compose up -d
```
