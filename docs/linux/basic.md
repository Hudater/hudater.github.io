---
tags:
  - Linux
  - Setup
title: Basic Linux Setup
description: Basic setup for Linux for both Desktop and Server
---
This page would define setup of basic installation with my bare minimum to get further the installation to either a server or desktop usecase

# SSH

## Fix SSH permissions after stow

```bash
sudo chmod 755 /home/$USER
sudo chmod 755 /home/$USER/GitIt -R
sudo chmod 700 /home/$USER/GitIt/SyncFiles/dotsPr/ssh/.ssh
sudo chmod 600 /home/$USER/GitIt/SyncFiles/dotsPr/ssh/.ssh/*
```

# fstab