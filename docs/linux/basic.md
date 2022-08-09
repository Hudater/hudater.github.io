---
tags:
  - Linux
  - Setup
title: Basic Linux Setup
description: Basic setup for Linux for both Desktop and Server
---

This page would define setup of basic installation with my bare minimum to get further the installation to either a server or desktop usecase

!!! note
    These steps are to be performed on all Linux installations

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

## SSH

### Fix SSH permissions after stow

```bash
sudo chmod 755 /home/$USER && \
sudo chmod 755 /home/$USER/GitIt -R && \
sudo chmod 700 /home/$USER/GitIt/SyncFiles/dotsPr/ssh/.ssh && \
sudo chmod 600 /home/$USER/GitIt/SyncFiles/dotsPr/ssh/.ssh/*
```

### SELinux

If SELinux is enforcing, allow SSH policy

```bash
restorecon -R -v /home/$USER/.ssh
```

## Shell

### Sh symlink

- [x] Ubuntu already comes with sh pointing to dash

- [x] For Arch, install `dashbinsh` from AUR
```bash
yay -S dashbinsh
```

### User Shell to ZSH

- [x] To change shell to zsh
```bash title="Run as user whose shell you want to change, NOT ROOT OR SUDO"
chsh -s $(which zsh)
```

!!! note
    If you're using bash and your bashrc won't load, [read this](https://stackoverflow.com/questions/18393521/bashrc-not-loading-until-run-bash-command/18393620#18393620){:target="_blank" rel="noopener noreferrer"}  
    Happened to me on Pi-Zero

### Env vars

## Swap