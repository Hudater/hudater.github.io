---
tags:
  - Bare Metal
  - Networking
title: Cloudflare Tunnel
description: Cloudflare Tunnel
---
### Description

Cloudflare Tunnel provides you with a secure way to connect your resources to Cloudflare without a publicly routable IP address

!!! todo
    Create a script to update cloudflared  
    Look into docker deployment by crazymax

## Bare Metal

### Basic info

!!! base-info "Basic info with official links"

    - [x] Package: `cloudflared`
    - [x] Other packages: `wget` `tar`
    - [x] Repo: [Github](https://github.com/cloudflare/cloudflared "qBittorrent Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Wiki](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/ "qBittorrent official Docs"){:target="_blank" rel="noopener noreferrer"}

### Getting started

!!! note
    These scripts are written by me and tested only on my personal environment  
    Use at your own risk

#### Installation and Uninstallation scripts

- [x] Use this script to install `cloudflared` on Debian, Ubuntu etc
  ```bash title="debInstall.sh"
  #!/bin/bash
  if (( $EUID != 0 )); then
    printf "This script must be run as root"
    exit 1
  fi

  if [ $(which cloudflared) ]; then
    printf "Cloudflare is already installed"
    exit 0;
  else

    cpuArch=$(uname -m)

    #checking if cloudflared dir exists in root's home dir
    if [ -d "/root/.cloudflared" ]; then
      printf "Moving /root/.cloudflared to /root/.cloudflared-BAK\n"
      mv /root/.cloudflared /root/.cloudflared-BAK
    fi

    # directory setup
    mkdir -p /root/.cloudflared
    chmod -R 777 /root/.cloudflared
    
    # assigning wget with options and url to var
    downloader="wget --no-check-certificate --content-disposition -P /root/.cloudflared/ https://github.com/cloudflare/cloudflared/releases/latest/download/"

    # downloading relevant package based on architecture
    # https://gist.github.com/jwebcat/5122366
    case $cpuArch in

      amd64 | x86_64)
        ${downloader}cloudflared-linux-amd64.deb
        ;;

      x86)
        ${downloader}cloudflared-linux-386.deb
        ;;

      aarch64 | arm64 | armv8)
        ${downloader}cloudflared-linux-arm64.deb
        ;;

      armv6l | armv6 | arm)
        ${downloader}cloudflared-linux-arm.deb
        ;;
    esac

    # Installing package
    printf "\nInstalling Cloudflared\n"
    sudo dpkg -i /root/.cloudflared/cloudflared-linux-*
    rm /root/.cloudflared/cloudflared-linux-*

    printf "\nIf this installation method failed for you, clean the files and build from source. For pi-zero, a build script is in the repo\n"
    unset cpuArch downloader
    exit 0;
  fi
  ```

- [x] Use this script to build `cloudflared` on Pi-Zero running Pi-OS
  ```bash title="buildArmhf.sh"
  #!/bin/sh
  if (( $EUID != 0 )); then
    printf "This script must be run as root"
    exit 1;
  fi

  # checking if os is armhf; true==continue else exit
  if [[ $(uname -m) = "armv6l" || $(uname -m) = "armv6" || $(uname -m) = "arm" ]]; then

    # prep
    export GOLANG="$(curl -s https://go.dev/dl/ | awk -F[\>\<] '/linux-armv6l/ && !/beta/ {print $5;exit}')"
    # function declaration
    goInstallFn() {
    wget https://golang.org/dl/$GOLANG
    tar -C /usr/local -xzf $GOLANG
    rm $GOLANG
    unset GOLANG
    printf 'PATH=$PATH:/usr/local/go/bin \nGOPATH=$HOME/golang\n' >> ~/.profile
    source ~/.profile
    }
    
    cfTunnelInstall(){
      git clone https://github.com/cloudflare/cloudflared.git
      cd cloudflared
      make cloudflared
      go install github.com/cloudflare/cloudflared/cmd/cloudflared
      mv /root/cloudflared/cloudflared /usr/bin/cloudflared
    }

    #checking if golang is already installed
    source ~/.profile
    if [ $(which go) ]; then

      #Go and Cloudflare installation section
      # check credit for command
      # explaination: reversing the ouptut of curl then cutting by char 1-20 and displaying the output of non-matching patter and reversing output again
      goLatestVer="$(printf "$GOLANG" | rev | cut -b 1-20 --complement | rev)"
      installedVer="$(go version | cut -d ' ' -f 3)" # cutting the output to show 3rd field with delimiter==space

      if [[ "$goLatestVer" = "$installedVer" ]]; then
        printf "Latest version of go is installed. Building cloudflared now"
        if [ $(which cloudflared) ]; then
          printf "Cloudflare is already installed"
          unset goLatestVer installedVer
          exit 0;
        else
          cfTunnelInstall
          unset goLatestVer installedVer
          exit 0;
        fi
      else
        printf "Current Go installation is outtaded. Remove the old version and re-run script"
        unset goLatestVer installedVer
        exit 1;
      fi
    else
      printf "Installing Golang!"
      goInstallFn
      printf "Building cloudflare tunnel package now!"
      if [ $(which cloudflared) ]; then
        printf "Cloudflare is already installed"
        unset goLatestVer installedVer
        exit 0;
      else
        cfTunnelInstall
        unset goLatestVer installedVer
        exit 0;
      fi
    fi

  else
    printf "\nNot an armv6 system! Try: https://github.com/Hudater/services/blob/main/hardware/cloudflared/debInstall.sh"
    exit 1;
  fi 
  ```

- [x] Use this script to Uninstall `cloudflared`
  ```bash title="uninstall.sh"
  #!/bin/sh
  printf "WARNING: Use this script if tunnel is installed via CLI\n"

  printf "Enter A/a to delete tunnel and binary or B/b to just uninstall binary. Anything else to exit\n"
  read userInput
  case $userInput in
    A|a)
      printf "\nYour tunnels are:\n $(cloudflared tunnel list)\n"
      printf "\nEnter your tunnel name to delete: "
      read userInput
      printf $userInput
      cloudflared tunnel delete $userInput
      printf "\nUninstalling cloudflared binary!\n"
      dpkg -r cloudflared
      mv /root/.cloudflared /root/.cloudflared-BAK
      exit 0;
      ;;

    N|n)
      printf "\nYour tunnel is untouched.\n"
      printf "\nUninstalling cloudflared binary!\n"
      dpkg -r cloudflared
      mv /root/.cloudflared /root/.cloudflared-BAK
      exit 0;
      ;;

    *)
      printf "Invalid input. Exiting with code 1"
      exit 1;
      ;;
  esac
  ```

### Configuration

- [x] Tunnel deployment script
  ```bash
  #!/bin/sh
  if (( $EUID != 0 )); then
    echo "This script must be run as root"
    exit 1;
  fi

  printf "WARNING: Must have already logged in your cloudflare account in browser beofre running script\n"
  printf "If logged in, Enter Y/y to continue else any other key to exit\n"
  read contLogin
  case $contLogin in
    Y|y)
      if [ $(which cloudflared) ]; then
        cloudflared tunnel login

        printf "Enter name for your tunnel: "
        read tunName
        cloudflared tunnel create $tunName

        wget -O /root/.cloudflared/config.yml https://raw.githubusercontent.com/Hudater/services/main/hardware/cloudflared/config.yml
        read -s -n 1 -p "Copy tunnel UUID and press ENTER to open config file in nano"
        nano /root/.cloudflared/config.yml

        printf "Enter your desired hostname (ex: home.example.com): "
        read hostName
        cloudflared tunnel route dns $tunName $hostName

        cloudflared service install
        systemctl enable --now cloudflared.service

        unset contLogin tunName hostName
        exit 0;
      else
        printf "Cloudflared is not installed. Try: https://github.com/Hudater/services/blob/main/hardware/cloudflared/debInstall.sh\n"
      fi
      ;;

    *)
      printf "Exiting script with code 1\n"
      unset contLogin
      exit 1;
      ;;
  esac
  ```

- [x] Config file
  ```yaml title="config.yml"
  #example config by IBRACORP. Modified for a scirpt of mine
  #credit: https://docs.ibracorp.io/cloudflare-tunnel/#create-the-config.yaml
  tunnel: UUID
  credentials-file: /root/.cloudflared/UUID.json

  # forward all traffic to Reverse Proxy w/ SSL
  ingress:
    - service: https://REVERSEPROXYIP:PORT #example https://192.168.29.12:443
      originRequest:
        originServerName: optional-subdomain.enter-your-domain.com #examples: example.com, home.example.com, *.example.com
  ```