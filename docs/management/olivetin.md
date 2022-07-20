---
tags:
  - Bare Metal
  - Management
  - Command Runner
title: Olivetin
description: Web based command runner for pre-configured commands
---
## Description

OliveTin gives safe and simple access to predefined shell commands from a web interface.

## Bare Metal

## Basic info

!!! base-info "Basic info with official links"

    - [x] Package: `olivetin`
    - [x] Official Wiki: [Official Olivetin Wiki](https://docs.olivetin.app/ "Official Olivetin Wiki"){:target="_blank" rel="noopener noreferrer"}
    - [x] Official Github: [Official Github Repo](https://github.com/OliveTin/OliveTin "Official Github Repo"){:target="_blank" rel="noopener noreferrer"}

## config.yaml
```yaml
---
# There is a built-in micro proxy that will host the webui and REST API all on 
# one port (this is called the "Single HTTP Frontend") and means you just need 
# one open port in the container/firewalls/etc. 
#
# Listen on all addresses available, port 1337
listenAddressSingleHTTPFrontend: 0.0.0.0:1337

# Choose from INFO (default), WARN and DEBUG
logLevel: "INFO"

# Actions (buttons) to show up on the WebUI:
actions:

- title: Restart Computers
  icon: "&#10226;"
  shell: ssh "{{ computers }}" 'sudo reboot'
  arguments:
    - name: computers
      choices:
        - title: Acer
          value: yourUserName@acer.lan

        - title: Main PC
          value: yourUserName@pc.lan

        - title: Pi4
          value: yourUserName@pi.lan

        - title: Pi Zero
          value: yourUserName@pero.lan

- title: Poweroff Computers
  icon: "&#9211;"
  shell: ssh "{{ computers }}" 'sudo poweroff'
  arguments:
    - name: computers
      choices:
        - title: Acer
          value: yourUserName@acer.lan

        - title: Main PC
          value: yourUserName@pc.lan

        - title: Pi4
          value: yourUserName@pi.lan

        - title: Pi Zero
          value: yourUserName@pero.lan

- title: Boot Computers
  icon: "&#129406;"
  shell: wakeonlan "{{ computers }}"
  arguments:
    - name: computers
      choices:
        - title: Acer
          value: fc:45:96:e7:cf:08

        - title: Main PC
          value: 18:c0:4d:39:0c:ff

- title: Monitor Functions
  icon: "&#128437;"
  shell: ssh yourUserName@pc.lan 'xset -display :0 dpms force "{{ function }}"'
  arguments:
    - name: function
      choices:
        - title: Turn Monitor On
          value: "on"

        - title: Turn Monitor Off
          value: "off"

- title: Restart AdGuard
  shell: systemctl restart AdGuardHome.service
  icon: "&#8962;"
  timeout: 5

- title: Restart Olivetin
  shell: systemctl restart OliveTin
  icon: "&#129746;"
  timeout: 5

```
## Setup

### Prerequisite

#### SSH setup

- [x] Copy ssh directory to root user's home direrctory
  ```bash
  sudo cp -r /home/$USER/.ssh /root/.ssh
  ```

- [x] Switch to root user
  ```bash
  sudo su
  ```

- [x] Fix the permissions
  ```bash
  chmod 700 /root/.ssh
  chmod 600 /root/.ssh/authorized_keys
  ```

- [x] SSH into all the machines once
  ```bash title="Copy paste from config.yaml"
  ssh yourUserName@acer.lan
  ```

- [x] Make some commands passwordless with `sudo visudo`
  ```bash title="Paste before the last line"
  ALL ALL=NOPASSWD: /sbin/poweroff,/sbin/reboot,/sbin/shutdown
  ```

#### Wake on LAN

!!! warning
    Ticked options are to be run on clients and unticked on server  
    Refer to Arch Wiki for more information on WoL [Arch Wiki for WoL](https://wiki.archlinux.org/title/Wake-on-LAN){:target="_blank" rel="noopener noreferrer"}

- [ ] Install `wakeonlan` package
  ```bash
  sudo apt install wakeonlan -y
  ```

- [x] Enable wakeonlan function at firmware level on clients

- [x] Find interface name
  ```bash title="Here enp1s0 is primary NIC"
  ip a
  1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
  2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
      link/ether fc:55:55:55:55:55 brd ff:ff:ff:ff:ff:ff
      inet 192.168.29.11/24 metric 100 brd 192.168.29.255 scope global dynamic enp1s0
        valid_lft 47171sec preferred_lft 47171sec
  ```

- [x] Create systemd-service for WoL
  ```bash title="sudoedit /etc/systemd/system/wol@.service"
  [Unit]
  Description=Wake-on-LAN for %i
  Requires=network.target
  After=network.target

  [Service]
  ExecStart=/usr/sbin/ethtool -s %i wol g
  Type=oneshot

  [Install]
  WantedBy=multi-user.target
  ```

- [x] Enable WoL service
  ```bash
  sudo systemctl enable wol@interface --now
  ```

- [x] Reboot twice

- [x] Check WoL status
  ```bash title="Wake-on: g means WoL is enabled"
  sudo ethtool enp1s0 | grep Wake-on
  ```

### Installation

!!! warning
    For some reason, setup didn't go well when doing all this via root user so I'm running these as general user  
    This guide is written with a `Pi-Zero` ,running `Raspbian`, in mind


- [x] Download relevant package from Github
  ```bash title="Deb packge for Armv6"
  wget -O OliveTin.deb https://github.com/OliveTin/OliveTin/releases/download/2022-04-07/OliveTin_2022-04-07_linux_armv6.deb
  ```

- [x] Make the pkg executable
  ```bash
  sudo chmod +x OliveTin.deb
  ```

- [x] Install the package:
  ```bash
  sudo dpkg -i OliveTin.​deb
  ```

- [x] Make a backup copy of default config
  ```bash
  sudo mv /etc/OliveTin/config.yaml /etc/OliveTin/BAK.config.yaml
  ```

- [x] Copy modified config file
  ```bash
  sudo cp /home/$USER/GitIt/SyncFiles/configBak/olivetin/config.yaml /etc/OliveTin/config.yaml
  ```

- [x] Start and enable OliveTin service
  ```bash
  sudo systemctl enable OliveTin.service --now
  ```

- [x] Check service status
  ```bash
  sudo systemctl status OliveTin.service
  ```


!!! tip
    Mount `/etc/OliveTin` via ssh to edit config file
    ```bash
    sshfs root@192.168.29.12:/etc/OliveTin Bench/OliveTin
    ```
