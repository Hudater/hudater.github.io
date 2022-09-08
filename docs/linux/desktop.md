---
tags:
  - Linux
  - Setup
  - Desktop
title: Desktop Linux Setup
description: Setup for desktop running Linux
---
This page would define additional setup for my Desktop instance

!!! info
    Run these using your standard user with sudo if required  

## Fonts
- [x] CD into `Bench` directory
```bash
cd ~/Bench
```
- [x] Clone Fonts repo
```bash
git clone git@github.com:Hudater/Fonts.git
```
- [x] Copy fonts to global fonts repo
```bash
sudo find Fonts/* -maxdepth 0 -type f,d ! -name 'LICENSE' ! -name 'README.md' -exec cp -rt /usr/local/share/fonts/ {} +
```
- [x] Re-generate Font cache
```bash
sudo fc-cache -f -v
```

## FStab
!!! warning
    FStab is mounted serial-wise, so lookout for depending mounts  
    Install `samba` package before further changes

- [x] Create mount folders with correct persmissions
```bash
sudo mkdir -pv /lab/{mdroot,piroot} &&\
sudo chown $USER:$USER /lab -R
```

- [x] Add SMB mount commands after mounting root, boot, home and other system partitions
```bash title="sudoedit /etc/fstab"
##########################SMB#############################
#
#
###Media server smb server mount
#
//media.lan/mdroot  /lab/mdroot  cifs username=USER,password=smbPassword,uid=1000,gid=1000,workgroup=workgroup  0 2
#
##rpi4 smb server mount
#
//pi.lan/piroot  /lab/piroot cifs  username=USER,password=smbPassword,uid=1000,gid=1000,workgroup=workgroup 0 2
```

- [x] Mount newly added SMB mounts
```bash
sudo mount -a
```

## Screenshot
- [x] Install `scrot`
```bash
paru -S scrot
```
- [x] Make `Pictures` directory
```bash
mkdir -pv ~/Pictures
```

## rEFInd Bootloader
### AMD Ucode
- [x] Install `amd-ucode`
```bash
paru -S amd-ucode
```
- [x] Add kernel and ucode to end of boot parameters

```bash
initrd=\initramfs-linux-zen.img #(1)
initrd=\amd-ucode.img #(2)
```

1. Secify kernel initram from `/boot`
2. CPU Microcode from `/boot`

### Kernel Parameters
- [x] Edit refind.conf with desired kernel parameter

```bash title="sudoedit /boot/refind_linux.conf"
amdgpu.ppfeaturemask=0xfffd7fff #(1)
ipv6.disable=1 #(2)
```

1. Enable extra controls over GPU for overclocking
2. Disables IPv6

!!! tip
    Final `refind_linux.conf` for me look like  
    ```bash
    "Boot with minimal options"   "ro root=PARTUUID=32768bfc-d092-224b-b36e-0b415dcf40c5 amdgpu.ppfeaturemask=0xfffd7fff ipv6.disable=1 initrd=\initramfs-linux-zen.img initrd=\amd-ucode.img"
    ```

### Theme
- [x] CD into `Bench` directory
```bash
cd ~/Bench
```
- [x] Clone theme
```bash
git clone git@github.com:Hudater/rEFInd-glassy.git
```
- [x] Create directory for theme in `/boot`
```bash
sudo mkdir /boot/EFI/BOOT/themes
```
- [x] Copy theme to that directory
```bash
sudo cp -r rEFInd-glassy /boot/EFI/BOOT/themes
```
- [x] Change resolution and reference theme in `refind.conf`
```bash
sudo tee -a /boot/EFI/BOOT/refind.conf > /dev/null <<EOT
resolution 1920 1080
include themes/rEFInd-glassy/theme.conf
EOT
```