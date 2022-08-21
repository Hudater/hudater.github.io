---
tags:
  - Linux
  - Setup
  - Server
title: Server Linux Setup
description: Setup for server running Linux
---
This page would define additional setup for Server instances

!!! info
    Run these using your standard user with sudo if required  

## Package Manager

### Basic Setup
=== "Debian/Ubuntu"

    - [x] Install Nala to replace apt:
    [Official Nala Installation Guide](https://gitlab.com/volian/nala#installation "Official Guide"){:target="_blank" rel="noopener noreferrer"}

    - [x] Enable unattended-upgrades: [PhoenixNap tutorial](https://phoenixnap.com/kb/automatic-security-updates-ubuntu){:target="_blank" rel="noopener noreferrer"}

=== "Fedora"

    - [x] Edit dnf config
    ```bash
    sudoedit /etc/dnf/dnf.conf
    ```

    - [x] Change or add these options
    ```bash
    fastestmirror=true
    max_parallel_downloads=10
    defaultyes=true
    ```

    - [x] Enable fusion repos:
    [Official Guide by Fedora](https://docs.fedoraproject.org/en-US/quick-docs/setup_rpmfusion/#proc_enabling-the-rpmfusion-repositories-using-command-line-utilities_enabling-the-rpmfusion-repositories){:target="_blank" rel="noopener noreferrer"}

### Install basic packages
```bash
sudo nala install ffmpeg git htop linux-generic nano ncdu neofetch neovim pv rsync stow tldr traceroute unrar unzip tar vim wget curl zsh
```

## Kernel Parameters

### GRUB bootloader
- [x] TODO
```bash title="sudoedit /boot/fileToAdd.conf"
TODO
```