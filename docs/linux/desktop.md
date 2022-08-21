---
tags:
  - Linux
  - Setup
  - Desktop
title: Desktop Linux Setup
description: Setup for desktop running Linux
---
This page would define additional setup for Desktop instances

!!! info
    Run these using your standard user with sudo if required  

## rEFInd Bootloader

### Kernel Parameters
- [x] Edit refind.conf with desired kernel parameter
```bash title="sudoedit /boot/refind_linux.conf"
"Boot with minimal options"   "ro root=PARTUUID=b61a3a3d-c9e6-1048-8c73-61bce9ebe201 amdgpu.ppfeaturemask=0xfffd7fff ipv6.disable=1"
```

!!! note
    Above config adds amdgpu option for overclocking via `corectrl` etc and disables IPv6

## Fonts
!!! todo
    Find out required fonts and add them to git repo

## FStab
!!! warning
    FStab is mounted serial-wise, so lookout for depending mounts

- [x] Create mount folders with correct persmissions
```bash
sudo mkdir -pv /lab/{acerroot,piroot} &&\
sudo chown $USER:$USER /lab -R
```

- [x] Add SMB mount commands after mounting root, boot, home and other system partitions
```bash
##########################SMB#############################
#
#
###Acer laptop smb server mount
#
//acer.lan/acerroot  /lab/acerroot  cifs username=USER,password=smbPassword,uid=1000,gid=1000,workgroup=workgroup  0 2
#
##rpi4 smb server mount
#
//pi.lan/piroot  /lab/piroot cifs  username=USER,password=smbPassword,uid=1000,gid=1000,workgroup=workgroup 0 2
```

## Package Manager

### Basic Setup
=== "Arch"

    - [x] Edit pacman config
    ```bash
    sudoedit /etc/pacman.conf
    ```

        - [x] Enable Parallel Downloads and Colors
        ```bash
        Color
        ILoveCandy
        ParallelDownloads = 5
        ```

        - [x] Enable Multilib and chaotic-aur
        ```bash
        [multilib]
        Include = /etc/pacman.d/mirrorlist

        [chaotic-aur]
        Include = /etc/pacman.d/chaotic-mirrorlist
        ```

    - [x] Edit Paru config
    ```bash
    sudoedit /etc/paru.conf
    ```

        - [x] Make results Bottom-Up just like `yay`
        ```bash
        BottomUp
        ```

    - [x] Fix mirrors with `reflector`
    ```bash title="Install reflector first"
    sudo reflector --latest 50 --protocol http --protocol https --sort rate --save /etc/pacman.d/mirrorlist
    ```

    - [ ] For Manjaro
    ```bash
    sudo pacman-mirrors --fasttrack && sudo pacman -Syyu
    ```

### Install basic packages
!!! todo
    Find basic packages for Arch and AwesomeWM
