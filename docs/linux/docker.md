---
tags:
  - Linux
  - Setup
  - Server
  - Docker
  - Desktop
title: Docker Setup on Linux
description: Setup for Docker on Linux
---
This page would define installation, uninstallation and user setup for Docker

!!! info
    Run these using your standard user with sudo if required  


## Docker

### Installation and Uninstallation

!!! warning
    Best way would be to follow [Official Docker Guide](https://docs.docker.com/engine/install/ubuntu/){:target="_blank" rel="noopener noreferrer"}

#### Docker Engine and Compose Plugin

- [x] Install pre-requisite packages
```bash
sudo apt-get update &&\
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

- [x] GPG Key
```bash
sudo mkdir -p /etc/apt/keyrings &&\
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

- [x] Add Repo URL to apt sources
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

- [x] Finally, Install docker engine
```bash
sudo apt-get update &&\
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

- [x] Add User to Docker group
```bash
sudo usermod -aG docker $USER
```

- [x] Enable and Run docker service
```bash
sudo systemctl enable --now docker.service
```

!!! warning
    Log out and log back in to make sure everything works fine

#### Uninstallation

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```


### Environment variables

!!! warning
    Official way of doing environment variables in docker-compose is via [`.env file`](https://docs.docker.com/compose/environment-variables/#the-env-file){:target="_blank" rel="noopener noreferrer"}  
    This way makes it easier for me to backup env vars with compose files without exposing secrets

- [x] Create `.zshenv` file in your home directory and create environment variables with following format
```bash title="vim ~/.zshenv"
export "CFG_DIR"="/home/user/.config"
export "varName"="varValue"
```

- [x] Restart shell or source file
```bash
source ~/.zshenv
```

- [x] Test variables with echo
```bash
echo $CFG_DIR
```