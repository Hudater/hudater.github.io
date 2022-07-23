---
tags:
  - Docker
  - Networking
title: WgEasy
description: Wireguard with Admin GUI
---
### Description

The easiest way to run WireGuard VPN + Web-based Admin UI

!!! warning
    You need to expose Ingress Port on your Router for `UDP` traffic

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `51821` `51822`
    - [x] Ingress Port: `51820`
    - [x] Image: [DockerHub](https://hub.docker.com/r/weejewel/wg-easy "qBittorrent image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/WeeJeWel/wg-easy "qBittorrent Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Wiki](https://github.com/WeeJeWel/wg-easy#options "qBittorrent official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker compose.yml

=== "Primary"

    ```yaml
    ---
    version: "3.8"
    services:
      wgpri:
        image: weejewel/wg-easy:latest
        container_name: wgpri
        networks:
          - proxy # rename this to your custom docker network
        environment:
          - WG_HOST=${WG_PRI_DOMAIN}
          - PASSWORD=${WG_PRI_PASS}
          - WG_PORT=${WG_PRI_PORT}
          - WG_DEFAULT_DNS=${LOCAL_DNS}
            #      - WG_ALLOWED_IPS=192.168.15.0/24, 10.0.1.0/24 # doesn't work with this option enabled
        volumes:
          - ${CFG_DIR}/wgpri:/etc/wireguard
        labels:
          traefik.enable: true   # Enable Traefik reverse proxy for the Traefik dashboard.
          traefik.http.services.wgpri.loadbalancer.server.port: 51821
        ports:
          - "51820:51820/udp" #wg traffic port; match with WG_PORT
          - "51821:51821/tcp" #webgui port
        restart: unless-stopped
        cap_add:
          - NET_ADMIN
          - SYS_MODULE
        sysctls:
          - net.ipv4.ip_forward=1
          - net.ipv4.conf.all.src_valid_mark=1

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```

=== "Secondary"

    ```yaml
    ---
    version: "3"
    services:
      wgsec:
        image: weejewel/wg-easy:latest
        container_name: wgsec
        networks:
          - proxy # rename this to your custom docker network
        environment:
          - WG_HOST=${WG_SEC_DOMAIN}
          - PASSWORD=${WG_SEC_PASS}
          - WG_PORT=${WG_SEC_PORT}
          - WG_DEFAULT_DNS=${LOCAL_DNS}
            #      - WG_ALLOWED_IPS=192.168.15.0/24, 10.0.1.0/24 # doesn't work with this option enabled
        volumes:
          - ${CFG_DIR}/wgsec:/etc/wireguard
        ports:
          - "51822:51820/udp" #wg traffic port; match with WG_PORT
          - "51821:51821/tcp" #webgui port
        restart: unless-stopped
        cap_add:
          - NET_ADMIN
          - SYS_MODULE
        sysctls:
          - net.ipv4.ip_forward=1
          - net.ipv4.conf.all.src_valid_mark=1

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```

=== "Cloud"

    ```yaml
    ---
    version: "3"
    services:
      wgcloud:
        image: weejewel/wg-easy:latest
        container_name: wgcloud
        networks:
          - proxy # rename this to your custom docker network
        environment:
          - WG_HOST=${WG_CL_ZUR}
          - PASSWORD=${WG_CL_PASS}
          - WG_PORT=${WG_PRI_PORT}
          - WG_DEFAULT_DNS=1.1.1.1
            #      - WG_ALLOWED_IPS=192.168.15.0/24, 10.0.1.0/24 # doesn't work with this option enabled
        volumes:
          - ${CFG_DIR}/wgcloud:/etc/wireguard
        ports:
          - "51820:51820/udp" #wg traffic port; match with WG_PORT
          - "51821:51821/tcp" #webgui port
        # labels:
        #   traefik.enable: true   # Enable Traefik reverse proxy for the Traefik dashboard.
        #   traefik.http.services.wgpri.loadbalancer.server.port: 51821
        restart: unless-stopped
        cap_add:
          - NET_ADMIN
          - SYS_MODULE
        sysctls:
          - net.ipv4.ip_forward=1
          - net.ipv4.conf.all.src_valid_mark=1

    networks:
      proxy:    # rename this to your custom docker network.
        external: true
    ```


### deploy.sh

=== "Primary"

    ```bash
    mkdir -p "${CFG_DIR}"/wgpri
    docker compose up -d
    ```

=== "Secondary"

    ```bash
    mkdir -p "${CFG_DIR}"/wgsec
    docker compose up -d
    ```

=== "Cloud"

    ```bash
    mkdir -p "${CFG_DIR}"/wgcloud
    docker compose up - d
    ```
