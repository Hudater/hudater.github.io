---
tags:
  - Bare Metal
  - Fileserver
  - Files
title: Samba
description: File sharing via SMB/CIFS protocol
---
### Description

Samba is an implementation of SMB protocol. It serves as a general purpose file server in my LAN

!!! note

    This service is deployed on multiple machines

## Bare Metal

### Basic info

!!! base-info "Basic info with official links"

    - [x] Package: `samba`
    - [x] Official Wiki: [Official Samba Wiki](https://wiki.samba.org/index.php/Main_Page "Official Samba Wiki"){:target="_blank" rel="noopener noreferrer"}
    - [x] Arch Wiki: [Arch Wiki for Samba](https://wiki.archlinux.org/title/samba "Arch Wiki for Samba"){:target="_blank" rel="noopener noreferrer"}

### smb.conf

!!! warning
    Change interface name in `interfaces` of `[global]` section  
    Run `ip a` to find your ethernet port name

=== "Media Server"

    ```
    [global]
      workgroup = WORKGROUP
      min protocol = SMB2
      client min protocol = SMB2
      server string = Media SMB
      server role = standalone server
      log file = /usr/local/samba/var/log.%m
      max log size = 50
      dns proxy = no

    [mdroot]
      comment = Sharing root over samba for authed users
      path = /
      force user = hudater
      force group = hudater
      create mask = 0664
      force create mode = 0664
      directory mask = 0775
      force directory mode = 0775
      public = no
      writable = yes
      browsable = yes

    [mdmnt]
      comment = Sharing MergerFS Folder publicly with no read-write
      path = /mnt
      force user = hudater
      force group = hudater
      create mask = 0664
      force create mode = 0664
      directory mask = 0775
      force directory mode = 0775
      public = yes
      writable = no
      browsable = yes
    ```

=== "Pi"

    ```
    [global]
      workgroup = WORKGROUP
      min protocol = SMB2
      client min protocol = SMB2
      server string = Pinux SMB
      server role = standalone server
      log file = /usr/local/samba/var/log.%m
      max log size = 50
      bind interfaces only = yes
      interfaces = 127.0.0.0/8 lo eth0

    [pist]
      comment = Sharing SSD Stoage over samba for all users
      path = /mnt/Pi_Storage
      force user = yourUserName
      force group = yourUserName
      create mask = 0664
      force create mode = 0664
      directory mask = 0775
      force directory mode = 0775
      public = yes
      writable = no
      browsable = yes

    [piroot]
      comment = Sharing root over samba for authed users
      path = /
      force user = yourUserName
      force group = yourUserName
      create mask = 0664
      force create mode = 0664
      directory mask = 0775
      force directory mode = 0775
      public = no
      writable = yes
      browsable = yes
    ```

### Setup

- [x] Install samba package

    === "Arch"
        ```bash
        sudo pacman -Sy samba
        ```

    === "Debian/Ubuntu"
        ```bash
        sudo apt install samba
        ```

- [x] Copy smb config

    === "Media Server"
        ```bash
        sudo cp $SYNCFILES/configBak/samba/acer/acer.smb.conf /etc/samba/smb.conf
        ```

    === "Pi"
        ```bash
        sudo cp $SYNCFILES/configBak/samba/pi/pi.smb.conf /etc/samba/smb.conf
        ```

- [x] Set smb password for user
    ```bash
    sudo smbpasswd -a $USER
    ```

- [x] Start and enable smb

    === "Arch"
        ```bash
        sudo systemctl enable smb.service nmb.service --now
        ```

    === "Debian/Ubuntu"
        ```bash
        sudo systemctl enable smbd.service nmbd.service --now
        ```

- [ ] To restart service:

    === "Arch"
        ```bash
        sudo systemctl restart smb.service nmb.service --now
        ```

    === "Debian/Ubuntu"
        ```bash
        sudo systemctl restart smbd.service nmbd.service --now
        ```

!!! info
    To mount smb share, refer to [fstab guide](/linux/desktop/#fstab)