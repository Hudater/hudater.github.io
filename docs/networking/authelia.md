---
tags:
  - Docker
  - Networking
title: Authelia
description: Authelia
---
### Description

Authelia is an open-source authentication and authorization server. I run it alongside [Traefik](traefik.md "Reverse Proxy") to achieve Single Sign On for all my services

!!! todo
    This service utilises database. More about databases will be added later

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Port: `9091`
    - [x] Image: [DockerHub](https://hub.docker.com/r/authelia/authelia "Authelia image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/authelia/authelia "Authelia Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Wiki](https://www.authelia.com/configuration/prologue/introduction/ "Authelia official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: '3'
services:
  authelia:
    image: authelia/authelia:latest
    container_name: authelia
    volumes:
      - "${BAK_CFG_DIR}/authelia/:/config"
    networks:
      - proxy
    labels:
      traefik.enable: true
      traefik.http.routers.authelia.entrypoints: https
    expose:
      - 9091
    restart: unless-stopped
    environment:
      - TZ=${TZ}
    healthcheck:
      disable: true
    depends_on:
      - redis_authelia
      - mariadb_authelia

## Database section
  redis_authelia:
    container_name: redis_authelia
    image: redis:latest
    command: >
          --requirepass "${REDIS_AUTHELIA_PASS}"
    expose:
      - 6379
    volumes:
      - "${DB_CFG_DIR}/redis_authelia:/data/"
    environment:
      ALLOW_EMPTY_PASSWORD: "no"
      REDIS_PASSWORD: "${REDIS_AUTHELIA_PASS}"
    networks:
      - proxy
      - dbs
    restart: unless-stopped

  mariadb_authelia:
    container_name: mariadb_authelia
    image: linuxserver/mariadb:latest
    expose:
      - 3306
    volumes:
      - "${DB_CFG_DIR}/mariadb_authelia:/config"
    environment:
      MYSQL_ROOT_PASSWORD: "${MYSQL_AUTHELIA_ROOT_PASS}"
      MYSQL_ROOT_USER: root
      MYSQL_DATABASE: authelia
      MYSQL_USER: authelia
      MYSQL_PASSWORD: "${MYSQL_AUTHELIA_USER_PASS}"
    networks:
      - proxy
      - dbs
    restart: unless-stopped

networks:
  proxy:
    external: true
  dbs:
    external: true
```

### deploy.sh

```bash
mkdir -p "${BAK_CFG_DIR}"/authelia
sudo chmod -R 777 "${BAK_CFG_DIR}"/authelia/
mkdir -p "${DB_CFG_DIR}"/redis_authelia
mkdir -p "${DB_CFG_DIR}"/mariadb_authelia
docker compose up -d
```

### Configuration

There are two config files you need for authelia: [configuration.yml](#configurationyml) and [users_database.yml](#users_databaseyml)

#### configuration.yml

```yaml
---
## Use this website to generate secrets (256 bit minimum)
## https://www.allkeysgenerator.com/Random/Security-Encryption-Key-Generator.aspx

server:
  host: 0.0.0.0
  port: 9091

log:
  level: debug

theme: dark

# This secret can also be set using the env variables AUTHELIA_JWT_SECRET_FILE
jwt_secret: "JWT-Secret-gen-1"
default_redirection_url: https://DOMAIN.com

totp:
  disable: false
  issuer: authelia.com
  algorithm: sha1 #sha 256 doesn't work with authy
  digits: 8
  period: 30
  skew: 1
  secret_size: 32

duo_api:
  hostname: api-hostname.duosecurity.com # from your duo dashboard
  integration_key: integ-key-from-duo-dash
  secret_key: secret-key-from-duo-dash
  disable: false
  enable_self_enrollment: false

ntp:
  address: "time.cloudflare.com:123"
  version: 4
  max_desync: 3s
  disable_startup_check: false
  disable_failure: false


authentication_backend:
  file:
    path: /config/users_database.yml
    password:
      algorithm: argon2id
      iterations: 1
      salt_length: 16
      parallelism: 8
      memory: 1024

access_control:
  default_policy: deny
  rules:

  ## Bypass rules for domains
  - domain:
      - "authelia.DOMAIN.com" #otherwise you will be in loop
      - "subdomain.DOMAIN.com"
    policy: bypass

  ## Bypass api / triggers
  - domain: "*.DOMAIN.com"
    resources:
      - "^/api([/?].*)?$"
      - "^/identity.*$"
      - "^/triggers.*$"
      - "^/meshagents.*$"
      - "^/meshsettings.*$"
      - "^/agent.*$"
      - "^/control.*$"
      - "^/meshrelay.*$"
      - "^/wl.*$"
    policy: bypass

  ## Catch-all
  - domain: "*.DOMAIN.com"
    subject:
    - "group:admins"
    policy: two_factor

session:
  name: authelia_session
  domain: DOMAIN.com
  same_site: lax
  secret: "second-secret-key-from-site"
  expiration: 1h
  inactivity: 5m
  remember_me_duration: 2M
  redis:
    host: redis_authelia
    port: 6379
    password: redis-password-from-docker-compose
    database_index: 0 #change if using multiple services off single db
    maximum_active_connections: 10
    minimum_idle_connections: 0

regulation:
  max_retries: 3
  find_time: 10m
  ban_time: 12h

storage:
  encryption_key: "third-secret-key-from-site"
  mysql:
    host: mariadb_authelia
    port: 3306
    database: authelia
    username: authelia
    password: "mariadb-user-pass-from-docker-compose"

notifier:
  smtp:
    host: smtp.gmail.com
    port: 587
    username: email@gmail.com
    password: password-from-google-account-dashboard
    sender: authelia@DOMAIN.com
```

#### users_database.yml

```yaml
users:
    my_username:
        displayname: Display Name
        # Use this command to generate password
        # docker run --rm authelia/authelia:latest authelia hash-password 'authelia'
        # Password is Authelia
        password: "$argon2id$v=19$m=65536,t=1,p=8$cUI4a0E3L1laYnRDUXl3Lw$ZsdsrdadaoVIaVj8NltA8x4qVOzT+/r5GF62/bT8OuAs"
        email: email@gmail.com #reset password links are sent here
        groups:
            - admins
            - dev
```
