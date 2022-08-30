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
sudo mkdir -pv /lab/{acerroot,piroot} &&\
sudo chown $USER:$USER /lab -R
```

- [x] Add SMB mount commands after mounting root, boot, home and other system partitions
```bash title="sudoedit /etc/fstab"
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

- [x] Mount newly added SMB mounts
```bash
sudo mount -a
```

## rEFInd Bootloader
### Kernel Parameters
- [x] Edit refind.conf with desired kernel parameter
```bash title="sudoedit /boot/refind_linux.conf"
"Boot with minimal options"   "ro root=PARTUUID=b61a3a3d-c9e6-1048-8c73-61bce9ebe201 amdgpu.ppfeaturemask=0xfffd7fff ipv6.disable=1"
```

!!! note
    Above config adds amdgpu option for overclocking via `corectrl` etc and disables IPv6  
    Add or remove modules as your wish

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