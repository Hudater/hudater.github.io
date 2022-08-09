---
tags:
  - Docker
  - Monitoring
title: Homer
description: Simple Dashboard configured via YAML
---
### Description

A dead simple static HOMepage for your servER to keep your services on hand, from a simple yaml configuration file

!!! warning
    I'm using a custom theme by [WalkxCode](https://github.com/WalkxCode/Homer-Theme){:target="_blank" rel="noopener noreferrer"}. If you wish not to use it, remove respective section in [deploy.sh](#deploysh) file

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `888`
    - [x] Image: [DockerHub](https://hub.docker.com/r/b4bz/homer "Homer image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/bastienwirtz/homer "Homer Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Homer Wiki](https://github.com/bastienwirtz/homer#table-of-contents "Homer official Docs"){:target="_blank" rel="noopener noreferrer"}
    - [x] Theme: [Theme by WalkxCode](https://github.com/WalkxCode/Homer-Theme "Theme by WalkxCode"){:target="_blank" rel="noopener noreferrer"}
    - [x] Theme Customization: [On Github](https://github.com/walkxcode/homer-theme/blob/main/docs/extra-configuration.md "Theme Customization"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: "3"
services:
  homer:
    image: b4bz/homer:latest
    container_name: homer
    networks:
      - proxy
    labels:
      traefik.enable: true   # Enable Traefik reverse proxy for the Traefik dashboard.
    volumes:
      - /home/$USER/.config/homer:/www/assets
      - "${BAK_CFG_DIR}/homer/config.yml:/www/assets/config.yml"
      - "${BAK_CFG_DIR}/icons:/www/assets/manicons"
      - "${BAK_CFG_DIR}/wallpapers:/www/assets/wallpapers"
    ports:
      - 888:8080
    environment:
      - UID=${PUID}
      - GID=${PGID}
      - TZ=${TZ}
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
#!/bin/sh
# Creating directories to hold config files etc
mkdir -p /home/$USER/.config/homer
touch "${BAK_CFG_DIR}"/homer/config.yml
mkdir -p "${BAK_CFG_DIR}"/homer/manicons
# deploying container
docker compose up -d

# DON'T DO THIS IF YOU DON'T WANT CUSTOM THEME
# cloning custom theme for homer
git clone https://github.com/WalkxCode/Homer-Theme.git /home/$USER/Homer-Theme
sudo chmod -R 777 /home/$USER/Homer-Theme

# deleting default config.
sudo chmod -R 777 /home/$USER/.config/homer
find /home/$USER/.config/homer/* ! -name 'manicons' ! -name 'wallpapers' ! -name 'config.yml' -type d,f -exec rm -rf "{}" +

# moving custom theme files to homer dir
mv /home/$USER/Homer-Theme/assets/config.yml /home/$USER/Homer-Theme/assets/og-config.yml
mv /home/$USER/Homer-Theme/assets/* /home/$USER/.config/homer/

# removing the custom theme git dir
rm -rf /home/$USER/Homer-Theme
```