---
tags:
  - Extras
  - Templates
title: Templates for docs, links etc
description: Templates for docs, links etc
---
# This page consists of templates for docs, links etc

### Description
Give a basic description of the app here  


## Bare Metal

### Basic info

!!! base-info "Basic info with official links"

    - [x] Package: `Package Name`
    - [x] Official Wiki: [Official Program Wiki](https://docs.haops.dev/ "Official Program Wiki"){:target="_blank" rel="noopener noreferrer"}
    - [x] Arch Wiki: [Arch Wiki for Program](https://wiki.archlinux.org/ "Arch Wiki for Program"){:target="_blank" rel="noopener noreferrer"}

### Getting started

- [x] Install PackageName package

    === "Arch"
        ```bash
        sudo pacman -Sy packageName
        ```

    === "Debian/Ubuntu"
        [Official Syncthing Guide](https://apt.syncthing.net/ "Official Syncthing Guide"){:target="_blank" rel="noopener noreferrer" .md-button .md-button--primary}

- [x] Write further steps

## Docker
### Basic info
!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `80`
    - [x] Other Ports: `443`
    - [x] Image: [DockerHub](https://hub.docker.com/r/hudater "PROJECT image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/Hudater/hudater.github.io "PROJECTNAME's Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [docs.HAops.dev](https://docs.haops.dev "PROJECTNAME's official website"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml
```yaml title="Basic compose manifest"
---
version: "3"
services:
  serviceName:
    image: MAINTAINER/IMAGE:latest #(1)
    container_name: myContainer #(2)
    restart: unless-stopped
    volumes:
      - "${CFG_DIR}/containerCfgDir:/config" #(3)
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    ports:
      - 80:80 #(4)
    networks:
      - proxy #(5)
    labels:
      traefik.enable: true
      traefik.http.services.portainer.loadbalancer.server.port: portNumber #(6)

networks:
  proxy: #(7)
    external: true
```

1. No tag defaults to `latest`  
   Best practice is to test your stuff with new image and pin the container to that version
2. Name the container or docker would name it randomly  
   Name used by traefik to autogenerate route  
   i.e, `traefik.example.com`
3. Refer to [variables guide](/linux/server/#environment-variables)
4. Change port number on left side
5. Same custom docker network as traefik
6. Specific port for traefik to route traffic from for this container
7. Specify your custom networks location here

### deploy.sh
```bash
#!/bin/sh
mkdir -p ${CFG_DIR}/containerCfgDir #(1)
mkdir -p ${CFG_DIR}/containerCfgDir/{multiple,subdirs} #(2)
docker compose up -d
```

1. Refer to [variables guide](/linux/server/#environment-variables)  
2. Use braces for variables and subdirectory names. No quotes  

## Template for link
- [ExampleLink](#template-for-link "An example of link"){:target="_blank" rel="noopener noreferrer"}

```bash title="Link syntax"
- [DisplayName](linkPath "HoverText"){:target="_blank" rel="noopener noreferrer"}
```
