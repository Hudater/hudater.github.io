---
tags:
  - Linux
  - Setup
title: Basic Linux Setup
description: Basic setup for Linux for both Desktop and Server
---
This page would define setup of basic installation with my bare minimum to get further the installation to either a server or desktop usecase

!!! info
    Run these using your standard user with sudo if required  

## DotFiles
### Create Useful Directories
```bash
mkdir -pv ~/Bench/Mount ~/GitIt/SyncFiles ~/info
```
```bash title="Extra for Desktop"
mkdir -pv ~/GitIt/Wallpapers-PC ~/Downloads
```

### Syncthing
- [x] Install [Syncthing](/filesv/syncthing/)
- [x] Sync `~/GitIt/SyncFiles` folder with following ignore patterns
```bash
.git
dotsPr/ssh/.ssh/known_hosts
dotsPr/ssh/.ssh/known_hosts.old
db
configBak/authelia
configBak/authelia-cloud
configBak/fenrus
```
- [x] Sync `PC-Walls` with Desktops

### Symlink dotfiles
#### dots
=== "Media Server"
    ```bash title="cd ~/GitIt/SyncFiles/dots"
    stow -vt ~ debSvMisc/ bpytop/ htop/ neofetch/ nvim/ templates/ vim/ zsh/
    ```

=== "PC"
    ```bash title="cd ~/GitIt/SyncFiles/dots"
    stow -vt ~ alacritty/ awesome/ bpytop/ compton/ htop/ kitty/ neofetch/ nitrogen/ nvim/ pcMisc/ pcVol/ rofi/ templates/ theming/ vim/ zsh/
    ```

=== "Pi"
    ```bash title="cd ~/GitIt/SyncFiles/dots"
    stow -vt ~ bpytop/ htop/ neofetch/ nvim/ templates/ piMisc/ vim/ zsh/
    ```


#### dotsPr
=== "Media Server"
    ```bash title="cd ~/GitIt/SyncFiles/dotsPr"
    stow -vt ~ debSvHome/ env/ extras/ git/ ssh/
    ```

=== "PC"
    ```bash title="cd ~/GitIt/SyncFiles/dotsPr"
    stow -vt ~ env/ extras/ git/ pcHome/ redshift/ ssh/
    ```

=== "Pi"
    ```bash title="cd ~/GitIt/SyncFiles/dotsPr"
    stow -vt ~ env/ git/ extras/ piHome/ ssh/
    ```

### Vim Plug
- [x] Install Plug
=== "Vim"
    ```bash title="vim"
    curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
        https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
    ```
=== "NeoVim"
    ```bash title="nvim"
    sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
        https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
    ```

- [x] Install Plugins specified in config files
=== "Vim"
    - [x] Open `vim`
    - [x] Enter command mode by typing ++escape+colon++ 
    - [x] Type `PlugInstall` and hit ++enter++
=== "NeoVim"
    - [x] Open `nvim`
    - [x] Enter command mode by typing ++escape+colon++ 
    - [x] Type `PlugInstall` and hit ++enter++

## DNS
### systemd-resolved
```bash
sudo systemctl enable systemd-resolved.service --now
```

## NTP

- [x] Symlink `/etc/localtime`
```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
```
- [x] Enable timesync service
```bash
sudo systemctl enable systemd-timesyncd.service --now
```
- [ ] Check status
```bash
timedatectl
```

## Shell
### Sh symlink
=== "Arch"
    Install `dashbinsh` from AUR
    ```bash
    paru -S dashbinsh --noconfirm
    ```

=== "Debian/Ubuntu"
    Already comes with sh pointing to dash

### Starship Prompt
=== "Arch"
    ```bash
    sudo pacman -S starship --noconfirm
    ```

=== "Debian/Ubuntu"
    ```bash
    curl -sS https://starship.rs/install.sh | sh
    ```

### User Shell to ZSH
- [x] To change shell to zsh
```bash title="Run as user whose shell you want to change, NOT ROOT OR SUDO"
chsh -s $(which zsh)
```

!!! tip
    If you're using bash and your bashrc won't load, [read this](https://stackoverflow.com/questions/18393521/bashrc-not-loading-until-run-bash-command/18393620#18393620){:target="_blank" rel="noopener noreferrer"}  
    Happened to me on Pi-Zero

## Zram-Swap and Swappiness

### Installation
=== "Arch"

    - [x] Install directly from AUR
    ```bash
    paru -S zram-swap-git --noconfirm
    ```

    - [x] Start and Enable service
    ```bash
    sudo systemctl enable --now zram-swap.service
    ```

=== "Ubuntu"

    - [x] Install `linux-modules`

        === "x86_64"
            ```bash
            sudo nala install linux-generic -y
            ```

        === "rpi4"
            ```bash
            sudo nala install linux-modules-extra-raspi -y
            ```

    - [x] Clone `zram-swap` repo
    ```bash
    git clone https://github.com/foundObjects/zram-swap.git
    ```

    - [x] Install from repo's script
    ```bash
    cd zram-swap && sudo ./install.sh
    ```

    - [x] Remove `/swapfile` and `fstab`
    ```bash
    sudo swapoff /swap.img
    sudo vim /etc/fstab and remove swapfile #TODO sed it
    sudo rm /swap.img
    ```

### Swappiness value
```bash
sudo tee -a /etc/sysctl.d/99-swappiness.conf > /dev/null <<EOT
vm.swappiness=10
EOT
```

## Mkdocs Material
- [x] Install `python` and `pip`

    === "Arch"
        ```bash
        sudo pacman -Sy python python-pip --noconfirm
        ```

    === "Debian/Ubuntu"
        ```bash
        sudo apt update && sudo apt install python3 python3-pip -y
        ```

- [x] CD into mkdocs folder
    ```bash
    cd ~/GitIt/SyncFiles/hudater.github.io
    ```

- [x] Install requirements from file
    ```bash
    pip3 install -r docs/requirements.txt
    ```

# Not necessary but might be useful
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

## Sudo setup as your root user
```bash title="EDITOR=nvim visudo"
@includedir /etc/sudoers.d #(1)
Defaults        insults
Defaults        env_reset,timestamp_timeout=60 #(2)
%wheel ALL=(ALL) ALL #(3)
ALL ALL=NOPASSWD: /sbin/poweroff,/sbin/reboot,/sbin/shutdown
```

1. Move this line to top for passwordless commands to work
2. This CAN BE a security risk. Not suggested to use
3. Change `wheel` group to `sudo` if required

## User Setup
### Create user
```bash title="UID and GID is 1000"
sudo useradd -m -u 1000 -G sudo,plugdev,video,adm userName
```

### Change Username with home and group
```bash title="Change Usernames accordingly"
usermod -l newUserName -d /home/newUserName -m oldUserName
groupmod -n newUserName oldUserName
```
