---
tags:
  - Linux
  - Setup
title: Basic Linux Setup
description: Basic setup for Linux for both Desktop and Server
---

This page would define setup of basic installation with my bare minimum to get further the installation to either a server or desktop usecase

## Package Manager

### Arch

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

### Debian/Ubuntu

- [x] Install Nala to replace apt:
[Official Nala Installation Guide](https://gitlab.com/volian/nala#installation "Official Guide"){:target="_blank" rel="noopener noreferrer"}

## SSH

### Fix SSH permissions after stow

```bash
sudo chmod 755 /home/$USER
sudo chmod 755 /home/$USER/GitIt -R
sudo chmod 700 /home/$USER/GitIt/SyncFiles/dotsPr/ssh/.ssh
sudo chmod 600 /home/$USER/GitIt/SyncFiles/dotsPr/ssh/.ssh/*
```

## fstab

## Shell

### Env vars