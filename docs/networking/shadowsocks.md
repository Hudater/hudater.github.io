---
tags:
  - Bare Metal
  - Networking
title: Shadowsocks
description: SOCKS5 Proxy
---
### Description

Shadowsocks is a secure split proxy loosely based on SOCKS5

## Bare Metal

### Basic info

!!! base-info "Basic info with official links"

    - [x] Package: `shadowsocks-libev`
    - [x] Guide I Followed: [LinuxBabe.com](https://www.linuxbabe.com/ubuntu/shadowsocks-libev-proxy-server-ubuntu){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/shadowsocks/shadowsocks-libev "Shadowsocks Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Shadowsocks Wiki](https://shadowsocks.org/guide/what-is-shadowsocks.html "Shadowsocks official Docs"){:target="_blank" rel="noopener noreferrer"}

### Getting started

!!! warning
    This Guide assumes there's no firewall like UFW

!!! todo
    Move client-side to opnsense

#### Server Side

- [x] Install Shadowsocks-libev
    ```bash
    sudo apt update
    sudo apt install shadowsocks-libev
    ```

- [x] Edit `config.json` file
    ```json title="sudoedit /etc/shadowsocks-libev/config.json"
    {
      "server":["::1", "0.0.0.0"],
      "mode":"tcp_and_udp",
      "server_port":8969,
      "local_port":1080,
      "password":"PutALongPasswordHere",
      "timeout":60,
      "method":"chacha20-ietf-poly1305"
    }
    ```

- [ ] IPTables (Dumping here in case needed later)
    ```bash
    sudo iptables -I INPUT -p tcp --dport 8969 -j ACCEPT
    sudo iptables -I INPUT -p udp --dport 8969 -j ACCEPT
    ```
    - [ ] [To Persist iptables](https://www.cyberciti.biz/faq/how-to-save-iptables-firewall-rules-permanently-on-linux/){:target="_blank" rel="noopener noreferrer"}

- [x] Start and Enable service
    ```bash
    sudo systemctl enable shadowsocks-libev.service --now
    ```

- [x] Check service status
    ```bash
    sudo systemctl status shadowsocks-libev.service
    ```

!!! warning
    If shadowsocks service errors out due to lacking entropy, install `rng-tools` and run `sudo rngd -r /dev/urandom`

#### Client Side

- [x] Install Shadowsocks-libev
    ```bash
    sudo apt update
    sudo apt install shadowsocks-libev
    ```

- [x] Edit `location.json` file (Change `location` to your desired name for file)
    ```json title="sudoedit /etc/shadowsocks-libev/zurich.json"
    {
      "server":["server.ip.or.domain"],
      "mode":"tcp_and_udp",
      "server_port":8969,
      "local_address":"media.lan",
      "local_port":1080,
      "password":"samePasswordAsServerHere",
      "timeout":60,
      "method":"chacha20-ietf-poly1305"
    }
    ```

- [x] Start and Enable service
    ```bash
    sudo systemctl enable shadowsocks-libev-local@zurich.service --now
    ```

- [x] Check service status
    ```bash
    sudo systemctl status shadowsocks-libev-local@zurich.service
    ```

#### Prowlarr

!!! note
    Prowlarr connects to your [client](#client-side) on LAN

- [x] Go to `Settings -> Indexers`

- [x] Add a new SOCKS5 Proxy

- [x] Options to fill out
    ```bash
    Name= asDesired
    Tags= sonarr radarr lidarr #Applies to indexers with at least one matching tag
    Host= 192.168.29.11 #your local_address from location.json
    Port= 1080

    ## Leave Username and Password empty since we didn't set any on client
    ```

- [x] Go to `Settings -> Apps`
    - [ ] Put `Sync Level` to `Full Sync` for all the Applications