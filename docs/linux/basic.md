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

## Shell
### Sh symlink

=== "Ubuntu"
    Already comes with sh pointing to dash

=== "Arch"
    Install `dashbinsh` from AUR
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

## Sudo setup

- [x] Edit sudoers file
```bash title="sudo EDITOR=nvim visudo"
Defaults        insults
Defaults        env_reset,timestamp_timeout=60
%wheel ALL=(ALL) ALL
ALL ALL=NOPASSWD: /sbin/poweroff,/sbin/reboot,/sbin/shutdown
```

!!! note
    If reboot etc don't work without password then move `@includedir /etc/sudoers.d` line to top

## Zram-Swap and Swappiness
### Installation

=== "Arch"

    - [x] Install directly from AUR
    ```bash
    yay -S zram-swap-git
    ```

    - [x] Start and Enable service
    ```bash
    sudo systemctl enable --now zramd.service
    ```

=== "Ubuntu"

    - [x] Install `linux-modules`

        === "x86_64"
            ```bash
            sudo nala install linux-generic
            ```

        === "rpi4"
            ```bash
            sudo nala install linux-modules-extra-raspi
            ```

    - [x] Clone `zram-swap` repo
    ```bash
    git clone https://github.com/foundObjects/zram-swap.git
    ```

    - [x] Install from repo's script
    ```bash
    cd zram-swap && sudo ./install.sh
    ```

### Swappiness value

- [x] Create and edit `swappiness.conf` file
```bash title="sudoedit /etc/sysctl.d/99-swappiness.conf"
vm.swappiness=10
```
- [x] Restart OS
```bash
sudo reboot
```

- [x] Check swappiness value
```bash
sysctl vm.swappiness
```