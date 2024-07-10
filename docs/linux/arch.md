---
tags:
  - Linux
  - Installation
title: Arch Installation
description: Guide for my personaly arch install
---
# Step-by-step Installation guide for Arch Linux
This page outlines a basic installation of Arch upon which you can build your system

!!! info
    Having [Arch wiki](https://wiki.archlinux.org/){:target="_blank" rel="noopener noreferrer"} and [installation guide](https://wiki.archlinux.org/title/Installation_guide){:target="_blank" rel="noopener noreferrer"} open on your mobile or other computer can be useful

## Prerequisites

- [x] Download the [latest Arch ISO from official website](https://archlinux.org/download/){:target="_blank" rel="noopener noreferrer"}. Preferably via Torrent since it doesn't load servers unnecessarily
- [x] Copy ISO to your [Ventoy USB](https://www.ventoy.net/en/doc_start.html){:target="_blank" rel="noopener noreferrer"}
- [x] Lookup your [motherboard's BIOS key](https://www.tomshardware.com/reviews/bios-keys-to-access-your-firmware,5732.html){:target="_blank" rel="noopener noreferrer"} and boot to Arch Live Environment via Ventoy

## Basic setup before chroot
### Connect to internet

=== "WiFi"

    !!! note
        | PlaceHolder          | Description                  | Dummy Value         |
        | -------------------- | ---------------------------- | ------------------- |
        | `yourWiFiPassphrase` | Password for your WiFi       | 123456qwerty        |
        | `yourDeviceName`     | Your WiFi device on Computer | wlan0               |
        | `yourWifiSSID`       | Your WiFi name or SSID       | Hudater             |

    === "One Liner"

        - [x] Get your wireless device name
        ```bash title="Most likely wlan0"
        ip a
        ```
        - [x] Change values according to your setup and execute command
        ```bash
        iwctl --passphrase yourWiFiPassphrase station yourDeviceName connect yourWifiSSID
        ```
        - [ ] Command with Dummy Values
        ```bash
        iwctl --passphrase 123456qwerty station wlan0 connect Hudater
        ```

    === "Detailed Instructions"

        - [x] Invoke interactive prompt for iwd
        ```bash
        iwctl
        ```
        - [x] List wireless devices
        ```bash
        device list
        ```
        - [x] Scan initiation
        ```bash
        station yourDeviceName scan
        ```
        - [x] List all available WiFi networks
        ```bash
        station yourDeviceName get-networks
        ```
        - [x] Connect to desired network via SSID
        ```bash
        station yourDeviceName connect yourWifiSSID
        ```

=== "Hard wired"

    - [x] If you are hard-wired via Ethernet or Fiber to your computer, you will likely get an IP assigned via DHCP  
    - [x] Verify connection by pinging some website
    ```bash
    ping gnu.org
    ```
    - [x] If you don't have connection check your interface
    ```bash
    ip a
    ```
    - [x] If interface is down or doesn't have an IP, troubleshoot from [the wiki](https://wiki.archlinux.org/title/Network_configuration#Network_interfaces){:target="_blank" rel="noopener noreferrer"}

### Enable NTP for system clock
```bash
timedatectl set-ntp true
```

- [ ] Check if NTP is active
```bash
timedatectl
```

### Disk Setup
#### Partiton Disk

!!! warning
    For help, type `m` and `q` to quit without writing  
    `++keyName++` means keyName to be pressed. It's not rendered inside code block

- [x] Execute fdisk with destination drive

```bash
fdisk /dev/nvme0n1
```

- [x] Create new partition table. IT WILL ERASE WHOLE DISK

```bash
g
```

- [x] Create two new partitions for `/boot` and `/`

=== "BOOT"
    ```bash
    n
    ++enter++ #(1)
    ++enter++
    +512M #(2)
    t #(3)
    1
    ```

    1. Press ++enter++ twice
    2. 512MB `/boot` or `boot` partition
    3. Change partition type for `/boot` to `EFI`

=== "ROOT"
    ```bash
    n
    ++enter++ #(1)
    ++enter++
    +150G #(2)
    ```

    1. Press ++enter++ twice
    2. 150GB `/` or `root` partition

- [x] Write your changes

```bash
w
```

#### Make Filesystem

- [x] Run `lsblk` to find device names

```bash
lsblk
```
??? abstract "lsblk output"
    ```bash hl_lines="3 4"
    NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
    nvme0n1     259:0    0 465.8G  0 disk
    ├─nvme0n1p1 259:1    0   512M  0 part
    ├─nvme0n1p2 259:2    0   150G  0 part
    ```

- [x] Create filesystem on both partitions
=== "BOOT"
    ```bash
    mkfs.fat -F32 /dev/nvme0n1p1
    ```

=== "ROOT"
    ```bash
    mkfs.ext4 /dev/nvme0n1p2
    ```

#### Mount partitions

!!! warning
    Mount `ROOT` partition first

=== "ROOT"
    ```bash
    mount /dev/nvme0n1p2 /mnt
    ```

=== "BOOT"
    ```bash
    mkdir /mnt/boot
    mount /dev/nvme0n1p1 /mnt/boot
    ```

### Install base system
!!! note
    Change the kernel and kernel-headers if you desire so  
    Add or remove programs as you like

```bash
pacstrap -i /mnt base base-devel linux-zen linux-zen-headers linux-firmware vim git stow openssh mtools dosfstools networkmanager network-manager-applet wireless_tools wpa_supplicant dialog
```

### Generate FSTab
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

## After Chroot
```bash title="Chroot into the installation now"
arch-chroot /mnt
```

### Symlink localtime
```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
```

### Sync Hardware Clock
```bash
hwclock --systohc
```

### Localization and Host
#### Locale.gen
- [x] Uncomment desired locale [`en_US.UTF-8 UTF-8`]
```bash
vim /etc/locale.gen
```

!!! tip
    Search in vim using ++colon+slash++ then your locale i.e, `en_US` and press `n` to forward and uncomment `UTF-8` version between the file

#### Locale.conf
```bash
echo "LANG=en_US.UTF-8" >> /etc/locale.conf
```

#### Generate Locale
```bash
locale-gen
```

#### Hostname
```bash
echo "pc" >> /etc/hostname
```

#### Hosts
```bash title="vim /etc/hosts"
# Static table lookup for hostnames.
# See hosts(5) for details.
127.0.0.1   localhost.lan   localhost
::1         localhost.lan   localhost
127.0.0.1   pc.lan          pc
```

### Bootloader
#### Refind
- [x] Install packages
```bash
pacman -Sy refind efibootmgr
```

- [x] Install refind on drive
```bash title="Use BOOT Partition device name here"
refind-install --usedefault /dev/nvme0n1p1 --alldrivers
```

- [x] Generate refind config
```bash
mkrlconf
```

- [x] Edit refind config

!!! warning
    Change PARTUUID of root partition using `blkid`  

```bash title="vim /boot/refind_linux.conf"
"Boot with minimal options" "ro root=PARTUUID=32768bfc-d092-224b-b36e-0b415dcf40c5"
```

### Pacman

- [x] Edit pacman config
```bash
vim /etc/pacman.conf
```

- [x] Enable Parallel Downloads and Colors
```bash
Color
ILoveCandy
ParallelDownloads = 5
```

- [x] Enable Multilib support
```bash
[multilib]
Include = /etc/pacman.d/mirrorlist
```

- [ ] Fix mirrors with `reflector`
```bash title="Install reflector first"
sudo reflector --latest 50 --protocol http --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```

### Users and Sudo
- [x] Set root password
```bash
passwd root
```

- [x] Create standard user
```bash
useradd -m -u 1000 -G wheel hudater
```

- [x] Password for standard user
```bash
passwd hudater
```

- [x] Edit sudoers file

```bash title="EDITOR=vim visudo"
@includedir /etc/sudoers.d #(1)
Defaults        insults
Defaults        env_reset,timestamp_timeout=60 #(2)
%wheel ALL=(ALL) ALL
ALL ALL=NOPASSWD: /sbin/poweroff,/sbin/reboot,/sbin/shutdown
```

1. Move this line to top for passwordless commands to work
2. This CAN BE a security risk. Not suggested to use

### Services
<!-- TODO -->
systemd-timesyncd
sshd
Network-Manager

!!! success
    Basic Arch installation is complete here. Reboot now to your new installation

## Post Installation

!!! warning
    Install `chaotic-aur` after enabling [NTP](/linux/basic/#ntp)

### Wifi
<!-- TODO -->
sudo nmtui wifi

### Package Manager
=== "Chaotic-AUR"

    !!! warning
        Visit [official website](https://aur.chaotic.cx/){:target="_blank" rel="noopener noreferrer"} for latest installation steps if out-of-date here

    - [x] Install Primary key, Keyring and Mirrorlist
    ```bash
    sudo pacman-key --recv-key FBA220DFC880C036 --keyserver keyserver.ubuntu.com &&\
    pacman-key --lsign-key FBA220DFC880C036 &&\
    pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-keyring.pkg.tar.zst' 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-mirrorlist.pkg.tar.zst'
    ```

    - [x] Add Mirrorlist to `pacman.conf`
    ```bash title="vim /etc/pacman.conf"
    [chaotic-aur]
    Include = /etc/pacman.d/chaotic-mirrorlist
    ```

=== "Paru"
    - [x] Install Paru from Chaotic-AUR
    ```bash
    pacman -Sy paru
    ```

    - [x] Edit Paru config
    ```bash
    sudoedit /etc/paru.conf
    ```

    - [x] Uncomment these options
    ```bash
    BottomUp
    SudoLoop
    ```

### Packages
#### Basic packages
```bash
paru -Sy absolutely-proprietary alacritty android-file-transfer android-tools anydesk-bin arandr authy awesome bat bpytop brave-bin chromium corectrl cpufetch-git curl dialog discord dosfstools drawio-desktop-bin duf exa feh firefox ffmpeg gimp git go gparted htop imwheel iperf3 kitty libreoffice-still lshw lxappearance-gtk3 mpv mtools nano nautilus nautilus-copy-path ncdu neofetch neovim network-manager-applet networkmanager nfs-utils nitrogen nmap noto-fonts noto-fonts-emoji noto-fonts-extra ntfs-3g numlockx nvtop openbsd-netcat openssh otf-font-awesome picom-ibhagwan-git python-pip qt5-styleplugins qt5ct radeontop redshift reflector rofi rpi-imager-bin rsync samba scrot sshfs starship stow sxiv syncthing tldr traceroute ttf-font-awesome ttf-ms-fonts unrar unzip ventoy-bin vi vim volumeicon vscodium-bin vscodium-bin-marketplace wget wireless_tools woeusb wol wpa_supplicant xclip xorg xorg-xinit xterm youtube-dl zathura zathura-pdf-mupdf zsh --noconfirm
```
#### Audio Packages
```bash
paru -Sy pipewire pipewire-pulse pavucontrol wireplumber --noconfirm
```
#### GPU Drivers
##### AMD
```bash
paru -Sy xf86-video-amdgpu lib32-mesa vulkan-radeon lib32-vulkan-radeon libva-mesa-driver lib32-libva-mesa-driver vulkan-icd-loader lib32-vulkan-icd-loader --noconfirm
```

##### Intel
```bash
paru -Sy mesa lib32-mesa --noconfirm
```

!!! success
    Move onto [Basic](basic.md) setup guide now
