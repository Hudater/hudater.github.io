---
tags:
  - Docker
  - Networking
title: Traefik
description: Cloud-Native reverse proxy
---
### Description

Traefik is an open-source Edge Router that makes publishing your services a fun and easy experience

!!! note
    Traefik or reverse-proxy/proxy is a huge topic in itself. For best explaination on Engineering side, refer to [Hussein Nasser](https://www.youtube.com/watch?v=C6IL8tjwC5E&t=636s){:target="_blank" rel="noopener noreferrer"}  
    Other guides I recommend are [Techno Tim](https://www.youtube.com/watch?v=u6H-Qwf4nZA&t=539s){:target="_blank" rel="noopener noreferrer"} and [IBRACORP](https://www.youtube.com/watch?v=pU7JvIrthxg&t=534s){:target="_blank" rel="noopener noreferrer"}
## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Ingress Ports: `80` `443`
    - [x] Web-GUI Port: `8080`
    - [x] Image: [DockerHub](https://hub.docker.com/_/traefik "Traefik image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/traefik/traefik "Traefik Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Traefik Wiki](https://doc.traefik.io/traefik/ "Traefik official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: '3'
services:
  traefik:
    container_name: traefik
    image: traefik:latest
    security_opt:
      - no-new-privileges:true
    ports:
      - 80:80
      - 443:443
      - 8080:8080
    volumes:
      - "${BAK_CFG_DIR}/traefik/:/etc/traefik/"
    networks:
      - proxy # rename this to your custom docker network
    labels:
      traefik.enable: true   # Enable Traefik reverse proxy for the Traefik dashboard.
      traefik.http.routers.api.rule: Host(`traefik.${CF_DOMAIN}`)    # Define the subdomain for the traefik dashboard.
      traefik.http.routers.api.entryPoints: https    # Set the Traefik entry point.
      traefik.http.routers.api.service: api@internal    # Enable Traefik API.
    environment:
      CF_DNS_API_TOKEN: "${CF_DNS_API_TOKEN}"
      CF_API_EMAIL: "${CF_API_EMAIL}"
    restart: unless-stopped
    depends_on:
      - dockersocket

  dockersocket:
    container_name: dockersocket
    image: tecnativa/docker-socket-proxy
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - proxy
    environment:
      CONTAINERS: 1
      POST: 0
    privileged: true
    restart: unless-stopped

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p "${BAK_CFG_DIR}"/traefik
docker compose up -d
```


### Configuration

There are config files I use for Traefik configuration

#### traefik.yml

```yaml
global:
  checkNewVersion: true
  sendAnonymousUsage: false

serversTransport:
  insecureSkipVerify: true

entryPoints:
  http:
    address: :80
    forwardedHeaders:
      trustedIPs: &trustedIps
        - 173.245.48.0/20
        - 103.21.244.0/22
        - 103.22.200.0/22
        - 103.31.4.0/22
        - 141.101.64.0/18
        - 108.162.192.0/18
        - 190.93.240.0/20
        - 188.114.96.0/20
        - 197.234.240.0/22
        - 198.41.128.0/17
        - 162.158.0.0/15
        - 104.16.0.0/12
        - 172.64.0.0/13
        - 131.0.72.0/22
        - 2400:cb00::/32
        - 2606:4700::/32
        - 2803:f800::/32
        - 2405:b500::/32
        - 2405:8100::/32
        - 2a06:98c0::/29
        - 2c0f:f248::/32
    http:
      redirections:
        entryPoint:
          to: https
          scheme: https

  https:
    address: :443
    forwardedHeaders:
      trustedIPs: *trustedIps
    http:
      tls:
        certResolver: letsencrypt
        domains:
          - main: DOMAIN.com
            sans:
              - '*.DOMAIN.com'
      middlewares:
        - securityHeaders@file
        - auth@file

providers:
  providersThrottleDuration: 2s

  file:
    filename: /etc/traefik/fileConfig.yml
    watch: true

  docker:
    watch: true
    network: proxy
    defaultRule: "Host(`{{ index .Labels \"com.docker.compose.service\"}}.DOMAIN.com`)"
    swarmModeRefreshSeconds: 15s
    exposedByDefault: false
    endpoint: "tcp://dockersocket:2375" # Uncomment if you are using docker socket proxy

api:
  dashboard: true
  insecure: true

log:
  level: DEBUG

certificatesResolvers:
  letsencrypt:
    acme:
      email: email@gmail.com
      storage: /etc/traefik/acme.json
      dnsChallenge:
        provider: cloudflare
        # Used to make sure the dns challenge is propagated to the rights dns servers
        resolvers:
          - "1.1.1.1:53"
          - "1.0.0.1:53"
```

#### fileConfig.yml

```yaml
http:
  routers:
    adguard:
      entryPoints:
        - https
      rule: 'Host(`adguard.DOMAIN.com`)'
      service: adguard

    olivetin:
      entryPoints:
        - https
      rule: 'Host(`olivetin.DOMAIN.com`)'
      service: olivetin

    homer:
      entryPoints:
        - https
      rule: 'Host(`homer.DOMAIN.com`)'
      service: homer

  services:
    adguard:
      loadBalancer:
        servers:
          - url: http://192.168.29.12:80/

    olivetin:
      loadBalancer:
        servers:
          - url: http://192.168.29.12:1337/

    homer:
      loadBalancer:
        servers:
          - url: http://192.168.29.10:8080/

  ## MIDDLEWARES ##
  middlewares:

    auth:
      forwardauth:
        address: "http://authelia:9091/api/verify?rd=https://authelia.DOMAIN.com/" #chagne authelia:port to containerName:port
        trustForwardHeader: true
        authResponseHeaders:
          - "Remote-User"
          - "Remote-Groups"
          - "Remote-Name"
          - "Remote-Email"
  
    # Authelia basic auth guard
    auth-basic:
      forwardauth:
        address: "http://authelia:9091/api/verify?auth=basic"
        trustForwardHeader: true
        authResponseHeaders:
          - "Remote-User"
          - "Remote-Groups"
          - "Remote-Name"
          - "Remote-Email"

    # Security headers
    securityHeaders:
      headers:
        customResponseHeaders:
          X-Robots-Tag: "none,noarchive,nosnippet,notranslate,noimageindex"
          server: ""
          X-Forwarded-Proto: "https"
        sslProxyHeaders:
          X-Forwarded-Proto: https
        referrerPolicy: "strict-origin-when-cross-origin"
        hostsProxyHeaders:
          - "X-Forwarded-Host"
        customRequestHeaders:
          X-Forwarded-Proto: "https"
        contentTypeNosniff: true
        browserXssFilter: true
        forceSTSHeader: true
        stsIncludeSubdomains: true
        stsSeconds: 63072000
        stsPreload: true

tls:
  options:
    default:
      minVersion: VersionTLS12
      cipherSuites:
        - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305
        - TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305
```

#### acme.json

```text
Create this file with permissions 600 and keep it empty
```