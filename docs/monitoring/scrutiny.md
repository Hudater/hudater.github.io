---
tags:
  - Docker
  - Monitoring
title: Scrutiny
description: Hard Drive Monitoring
---
### Description

Hard Drive S.M.A.R.T Monitoring, Historical Trends & Real World Failure Thresholds

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `8780`
    - [x] Image: [DockerHub](https://hub.docker.com/r/linuxserver/scrutiny "Scrutiny image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/AnalogJ/scrutiny "Scrutiny Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Scrutiny Wiki](https://github.com/AnalogJ/scrutiny#configuration "Scrutiny official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker compose.yml

=== "Acer"

    ```yaml
    ---
    version: "2.1"
    services:
      scruacer:
        image: linuxserver/scrutiny:version-8e34ef8d
        container_name: scruacer
        networks:
          - proxy # rename this to your custom docker network
        cap_add:
          - SYS_RAWIO
          - SYS_ADMIN #optional
        environment:
          - PUID=${PUID}
          - PGID=${PGID}
          - TZ=${TZ}
          - SCRUTINY_API_ENDPOINT=http://localhost:8080
          - SCRUTINY_WEB=true
          - SCRUTINY_COLLECTOR=true
        volumes:
          - "${BAK_CFG_DIR}/scrutiny/scrutiny.yaml:/config/scrutiny.yaml"
          - "${CFG_DIR}/scrutiny:/config"
          - /run/udev:/run/udev:ro
        ports:
          - 8780:8080
        devices:
          - /dev/sda:/dev/sda
          - /dev/sdb:/dev/sdb
          - /dev/sdc:/dev/sdc
        restart: unless-stopped

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```

=== "PC"

    ```yaml
    ---
    version: "2.1"
    services:
      scrumain:
        image: linuxserver/scrutiny:latest
        container_name: scrumain
        networks:
          - proxy # rename this to your custom docker network
        cap_add:
          - SYS_RAWIO
          - SYS_ADMIN #optional
        environment:
          - PUID=${PUID}
          - PGID=${PGID}
          - TZ=${TZ}
          - SCRUTINY_API_ENDPOINT=http://localhost:8080
          - SCRUTINY_WEB=true
          - SCRUTINY_COLLECTOR=true
        volumes:
          - "${BAK_CFG_DIR}/scrutiny/scrutiny.yaml:/config/scrutiny.yaml"
          - "${CFG_DIR}/scrutiny:/config"
          - /run/udev:/run/udev:ro
        ports:
          - 8780:8080
        devices:
          - /dev/nvme0:/dev/nvme0
        restart: unless-stopped

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```

=== "Pi4"

    ```yaml
    ---
    version: "2.1"
    services:
      scrupi:
        image: linuxserver/scrutiny:version-8e34ef8d
        container_name: scrupi
        networks:
          - proxy # rename this to your custom docker network
        cap_add:
          - SYS_RAWIO
          - SYS_ADMIN #optional
        environment:
          - PUID=${PUID}
          - PGID=${PGID}
          - TZ=${TZ}
          - SCRUTINY_API_ENDPOINT=http://localhost:8080
          - SCRUTINY_WEB=true
          - SCRUTINY_COLLECTOR=true
        labels:
          traefik.enable: true
        volumes:
          - "${BAK_CFG_DIR}/scrutiny/scrutiny.yaml:/config/scrutiny.yaml"
          - "${CFG_DIR}/scrutiny:/config"
          - /run/udev:/run/udev:ro
        ports:
          - 8780:8080
        devices:
          - /dev/sda:/dev/sda
        restart: unless-stopped

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```

### deploy.sh

=== "Acer"

    ```bash
    mkdir -p "${CFG_DIR}"/scrutiny
    docker compose up -d
    echo "Sleeping for 5 seconds"
    sleep 5
    docker exec -it scruacer scrutiny-collector-metrics run
    ```

=== "PC"

    ```bash
    mkdir -p "${CFG_DIR}"/scrutiny
    docker compose up -d
    echo "Sleeping for 5 seconds"
    sleep 5
    docker exec -it scrumain scrutiny-collector-metrics run
    ```


=== "Pi4"

    ```bash
    mkdir -p "${CFG_DIR}"/scrutiny
    docker compose up -d
    echo "Sleeping for 5 seconds"
    sleep 5
    docker exec -it scrupi scrutiny-collector-metrics run
    ```