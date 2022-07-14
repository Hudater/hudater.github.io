---
tags:
  - Fileserver
  - Files
  - Sharing
title: Filebrowser
description: Web based file manager with file sharing and much more
---
### Description

Filebrowser is Web based file manager with basic auth, command runner, built-in file editor, file sharing with time-based expiry and much more  

!!! note

    This service is deployed on multiple machines. Non-primary config files are in drop-down

### Basic info

!!! base-info "Basic info with official links"

    - [x] Port: `420`
    - [x] Image: [DockerHub](https://hub.docker.com/r/filebrowser/filebrowser "Filebrowser image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/filebrowser/filebrowser "Filebrowser's Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [FileBrowser.org](https://filebrowser.org "Filebrowser's official website"){:target="_blank" rel="noopener noreferrer"}

### docker compose.yml

=== "Acer"

    ```yaml
    ---
    version: '3'
    services:
      fbacer:
        image: filebrowser/filebrowser:latest
        container_name: fbacer
        user: ${PUID}:${PGID}
        ports:
          - 420:80
        networks:
          - proxy # rename this to your custom docker network
        volumes:
          - ${DATA_DIR}:/srv
          - ${BAK_CFG_DIR}/filebrowser/fbacer/filebrowser.db:/database.db
        restart: unless-stopped
        security_opt:
          - no-new-privileges:true
        command: --noauth #disables auth, don't use this if not using third party auth like authelia

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```

=== "Cloud"

    ```yaml
    ---
    version: '3'
    services:
      fbcloud:
        image: filebrowser/filebrowser:latest
        container_name: fbcloud
        user: ${PUID}:${PGID}
        ports:
          - 420:80
        networks:
          - proxy # rename this to your custom docker network
        labels:
          traefik.enable: true
        volumes:
          - /:/srv
          - ${BAK_CFG_DIR}/filebrowser/fbcloud/filebrowser.db:/database.db
        restart: unless-stopped
        security_opt:
          - no-new-privileges:true

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```

=== "PC"

    ```yaml
    ---
    version: '3'
    services:
      fbmain:
        image: filebrowser/filebrowser:latest
        container_name: fbmain
        user: ${PUID}:${PGID}
        ports:
          - 420:80
        networks:
          - proxy # rename this to your custom docker network
        volumes:
          - /storage:/srv
          - ${BAK_CFG_DIR}/filebrowser/fbmain/filebrowser.db:/database.db
        restart: unless-stopped
        security_opt:
          - no-new-privileges:true
        command: --noauth #disables auth, don't use this if not using third party auth like authelia

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```

=== "Pi4"

    ```yaml
    ---
    version: '3'
    services:
      fbpi:
        image: filebrowser/filebrowser:latest
        container_name: fbpi
        user: ${PUID}:${PGID}
        ports:
          - 420:80
        networks:
          - proxy # rename this to your custom docker network
        labels:
          traefik.enable: true
        volumes:
          - /mnt/Pi_Storage/:/srv
          - ${BAK_CFG_DIR}/filebrowser/fbpi/filebrowser.db:/database.db
        restart: unless-stopped
        security_opt:
          - no-new-privileges:true
        command: --noauth #disables auth, don't use this if not using third party auth like authelia

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```

=== "Share"

    ```yaml
    ---
    version: '3'
    services:
      fbshare:
        image: filebrowser/filebrowser:latest
        container_name: fbshare
        user: ${PUID}:${PGID}
        ports:
          - 4290:80
        networks:
          - proxy # rename this to your custom docker network
        volumes:
          - ${DATA_DIR}:/srv
          - ${BAK_CFG_DIR}/filebrowser/fbshare/filebrowser.db:/database.db
        restart: unless-stopped
        security_opt:
          - no-new-privileges:true

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```


### deploy.sh

=== "Acer"

    ```bash
    mkdir -p "${BAK_CFG_DIR}"/filebrowser/fbacer
    touch "${BAK_CFG_DIR}"/filebrowser/fbacer/filebrowser.db
    docker compose up -d
    ```

=== "Cloud"

    ```bash
    mkdir -p "${BAK_CFG_DIR}"/filebrowser/fbcloud
    touch "${BAK_CFG_DIR}"/filebrowser/fbcloud/filebrowser.db
    docker compose up -d
    ```

=== "PC"

    ```bash
    mkdir -p "${BAK_CFG_DIR}"/filebrowser/fbmain
    touch "${BAK_CFG_DIR}"/filebrowser/fbmain/filebrowser.db
    docker compose up -d
    ```


=== "Pi4"

    ```bash
    mkdir -p "${BAK_CFG_DIR}"/filebrowser/fbpi
    touch "${BAK_CFG_DIR}"/filebrowser/fbpi/filebrowser.db
    docker compose up -d
    ```


=== "Share"

    ```bash
    mkdir -p "${BAK_CFG_DIR}"/filebrowser/fbshare
    touch "${BAK_CFG_DIR}"/filebrowser/fbshare/filebrowser.db
    docker compose up -d
    ```