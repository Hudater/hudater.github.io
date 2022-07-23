---
tags:
  - Bare Metal
  - Networking
title: Adguard
description: DNS Server with Ad-blocking
---
### Description

DNS Server with Ad-blocking just like Pi-hole

!!! note
    Currently using Bare-metal deployment since it occasionally misbehaved on docker

## Global Settings for Adguard

!!! note
    This section features scripts and settings to be used regardless the installation method

### Post Installation Configuration

- [x] DNS Settings
    - [ ] Set upstream DNS
      ```text
      https://dns10.quad9.net/dns-query
      tls://1dot1dot1dot1.cloudflare-dns.com
      https://cloudflare-dns.com/dns-query
      tls://dns-unfiltered.adguard.com
      https://dns-unfiltered.adguard.com/dns-query
      1.1.1.1
      8.8.8.8
      ```
    - [ ] Set resolution method to `Parallel Requests`
    - [ ] Test upstream and Apply

- [x] Allowlist
    - [ ] Create an allowlist like one [here](#allowlist) and push it to a public repo
    - [ ] Add link to raw version inside `Filters/DNS_Allowlists`
    ``` text
    https://raw.githubusercontent.com/Hudater/services/main/baremetal/adguard/allowlist.txt
    ```

- [x] Blocklist
    - [ ] Change IP address, username and password in [script](#batch-blocklist-script)
    - [ ] Install dependency for the script
    ```bash
    pip install requests
    ```
    - [ ] Run the script
    ```bash
    python batch_adlist.py
    ```

### Allowlist

```text title="allowlist.txt"
||s.youtube.com/^
||ravenminer.com^
||stratum.ravenminer.com^
||whattomine.com/^
||xmrig.com/^
||hiveos.farm/^
||download.hiveos.farm/^
||mega.nz/^
||mega.io^
||blog.jdpfu.com/^
||jdownloader.org/^
||rarbg.com/^
||1337x.to^
||1337xx.to/^
||1337xxx.to/^
||1377x.to/^
||1377xx.to/^
||1377xxx.to/^
||1377x.is/^
||tpb23.ukpass.co/^
||torlock2.com/^
||kickasstorrent.cr/^
||movietorrent.co/^
||badasstorrents.com/^
||bitsearch.to/^
||btdig.com/^
||glodls.to/^
||kickasstorrents.to/^
||kickass.ws/^
||movietorrent.co/^
||thepiratebay.org/^
||solidtorrents.to/^
||www.torlock.com/^
||torrent.by/^
||torrents-csv.ml/^
||2torrentz2eu.in/^
||yourbittorrent.com/^
||zooqle.com/^
```

### Batch Blocklist Script

!!! todo
  Look into [letsblockit](https://github.com/letsblockit/letsblockit)

```python title="batch_adlist.py"
## credit: https://www.reddit.com/user/carzian/
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.poolmanager import PoolManager
import ssl
import requests
import json

## DEPENDENCY:
# pip install requests

## CHANGE HERE ##
# ip address of AdGuard Home
# "http(s)://<adguardHomeIp:<port>"
# host = "http://your.instance.ip.address:port"
host = "http://192.168.29.12:80"
# user name
userName = "USERNAME_HERE"
# password
password = "PASSWORD_HERE"

# block list
# taken from Wally3K's Firebog https://firebog.net/
urls = [
"https://raw.githubusercontent.com/PolishFiltersTeam/KADhosts/master/KADhosts.txt"
"https://raw.githubusercontent.com/FadeMind/hosts.extras/master/add.Spam/hosts"
"https://v.firebog.net/hosts/static/w3kbl.txt"
"https://adaway.org/hosts.txt"
"https://v.firebog.net/hosts/AdguardDNS.txt"
"https://v.firebog.net/hosts/Admiral.txt"
"https://raw.githubusercontent.com/anudeepND/blacklist/master/adservers.txt"
"https://s3.amazonaws.com/lists.disconnect.me/simple_ad.txt"
"https://v.firebog.net/hosts/Easylist.txt"
"https://pgl.yoyo.org/adservers/serverlist.php?hostformat=hosts&showintro=0&mimetype=plaintext"
"https://raw.githubusercontent.com/FadeMind/hosts.extras/master/UncheckyAds/hosts"
"https://raw.githubusercontent.com/bigdargon/hostsVN/master/hosts"
"https://v.firebog.net/hosts/Easyprivacy.txt"
"https://v.firebog.net/hosts/Prigent-Ads.txt"
"https://raw.githubusercontent.com/FadeMind/hosts.extras/master/add.2o7Net/hosts"
"https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt"
"https://hostfiles.frogeye.fr/firstparty-trackers-hosts.txt"
"https://raw.githubusercontent.com/DandelionSprout/adfilt/master/Alternate%20versions%20Anti-Malware%20List/AntiMalwareHosts.txt"
"https://osint.digitalside.it/Threat-Intel/lists/latestdomains.txt"
"https://s3.amazonaws.com/lists.disconnect.me/simple_malvertising.txt"
"https://v.firebog.net/hosts/Prigent-Crypto.txt"
"https://bitbucket.org/ethanr/dns-blacklists/raw/8575c9f96e5b4a1308f2f12394abd86d0927a4a0/bad_lists/Mandiant_APT1_Report_Appendix_D.txt"
"https://phishing.army/download/phishing_army_blocklist_extended.txt"
"https://gitlab.com/quidsup/notrack-blocklists/raw/master/notrack-malware.txt"
"https://raw.githubusercontent.com/Spam404/lists/master/main-blacklist.txt"
"https://raw.githubusercontent.com/FadeMind/hosts.extras/master/add.Risk/hosts"
"https://urlhaus.abuse.ch/downloads/hostfile/"
"https://zerodot1.gitlab.io/CoinBlockerLists/hosts_browser"
"https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt"
"https://raw.githubusercontent.com/DandelionSprout/adfilt/master/Alternate%20versions%20Anti-Malware%20List/AntiMalwareAdGuardHome.txt"
"https://raw.githubusercontent.com/hoshsadiq/adblock-nocoin-list/master/hosts.txt"
"https://raw.githubusercontent.com/durablenapkin/scamblocklist/master/adguard.txt"
"https://raw.githubusercontent.com/mitchellkrogza/The-Big-List-of-Hacked-Malware-Web-Sites/master/hosts"
"https://curben.gitlab.io/malware-filter/urlhaus-filter-agh-online.txt"
"https://raw.githubusercontent.com/Perflyst/PiHoleBlocklist/master/SmartTV-AGH.txt"
"https://pgl.yoyo.org/adservers/serverlist.php?hostformat=adblockplus&showintro=1&mimetype=plaintext"
"https://v.firebog.net/hosts/AdguardDNS.txt",
"https://raw.githubusercontent.com/PolishFiltersTeam/KADhosts/master/KADhosts.txt",
"https://raw.githubusercontent.com/FadeMind/hosts.extras/master/add.Spam/hosts",
"https://v.firebog.net/hosts/static/w3kbl.txt",
"https://v.firebog.net/hosts/AdguardDNS.txt",
"https://v.firebog.net/hosts/Admiral.txt",
"https://raw.githubusercontent.com/anudeepND/blacklist/master/adservers.txt",
"https://s3.amazonaws.com/lists.disconnect.me/simple_ad.txt",
"https://v.firebog.net/hosts/Easylist.txt",
"https://pgl.yoyo.org/adservers/serverlist.php?hostformat=hosts&showintro=0&mimetype=plaintext",
"https://raw.githubusercontent.com/FadeMind/hosts.extras/master/UncheckyAds/hosts",
"https://raw.githubusercontent.com/bigdargon/hostsVN/master/hosts",
"https://v.firebog.net/hosts/Easyprivacy.txt",
"https://v.firebog.net/hosts/Prigent-Ads.txt",
"https://raw.githubusercontent.com/FadeMind/hosts.extras/master/add.2o7Net/hosts",
"https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt",
"https://hostfiles.frogeye.fr/firstparty-trackers-hosts.txt",
"https://raw.githubusercontent.com/DandelionSprout/adfilt/master/Alternate%20versions%20Anti-Malware%20List/AntiMalwareHosts.txt",
"https://osint.digitalside.it/Threat-Intel/lists/latestdomains.txt",
"https://s3.amazonaws.com/lists.disconnect.me/simple_malvertising.txt",
"https://v.firebog.net/hosts/Prigent-Crypto.txt",
"https://bitbucket.org/ethanr/dns-blacklists/raw/8575c9f96e5b4a1308f2f12394abd86d0927a4a0/bad_lists/Mandiant_APT1_Report_Appendix_D.txt",
"https://phishing.army/download/phishing_army_blocklist_extended.txt",
"https://gitlab.com/quidsup/notrack-blocklists/raw/master/notrack-malware.txt",
"https://raw.githubusercontent.com/Spam404/lists/master/main-blacklist.txt",
"https://urlhaus.abuse.ch/downloads/hostfile/",
"https://zerodot1.gitlab.io/CoinBlockerLists/hosts_browser",
"https://abl.arapurayil.com/filters/main.txt",
"https://abl.arapurayil.com/filters/nsfw.txt",
"https://www.fanboy.co.nz/fanboy-indian.txt",
"https://raw.githubusercontent.com/chadmayfield/my-pihole-blocklists/master/lists/pi_blocklist_porn_top1m.list"
]
allow_urls = []

############ End Edits #################

# Open TLSv1 Adapter
class MyAdapter(HTTPAdapter):
    def init_poolmanager(self, connections, maxsize, block=False):
        self.poolmanager = PoolManager(num_pools=connections,
                                       maxsize=maxsize,
                                       block=block,
                                       ssl_version=ssl.PROTOCOL_TLSv1)

headers = "{'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:52.0) Gecko/20100101 Firefox/52.0'}"     

s = requests.Session()
s.mount(host, MyAdapter())
x = s.post(host + "/control/login", json.dumps("{"name": userName, "password" : password}"), headers=headers )
print(x.text)

for u in urls:
	filterObj = json.dumps("{'url':u, "name":u,"whitelist":False}")
	print(filterObj)
	x = s.post(host + "/control/filtering/add_url", data = filterObj, headers=headers)
	print(x.text)

# help from
# https://stackoverflow.com/questions/30946370/using-requests-to-login-to-a-website-that-has-javascript-login-form
# https://stackoverflow.com/questions/33818206/python-login-into-a-website-with-javascript-form
```

## Bare Metal

### Basic info

!!! base-info "Basic info with official links"

    - [x] Package: `adguard`
    - [x] Other packages: `wget` `tar`
    - [x] Repo: [Github](https://github.com/AdguardTeam/AdGuardHome "Adguard Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Adguard Wiki](https://github.com/AdguardTeam/AdGuardHome/wiki "Adguard official Docs"){:target="_blank" rel="noopener noreferrer"}

### Getting started

!!! note
    These scripts are written by me and tested only on my personal environment  
    Use at your own risk

#### Installation script

```bash title="installation.sh"
#!/bin/sh
#checking if adguard dir exists in .config
if [ -d "/home/$USER/.config/adguard" ]; then
  echo "Moving /home/$USER/.config/adguard to /home/$USER/.config/adguard-BAK\n"
  mv /home/$USER/.config/adguard /home/$USER/.config/adguard-BAK
fi

#checking if tar is installed

if ! command -v tar >/dev/null 2>&1
then
  echo >&2 "tar is not installed. Enter Y to install tar or Q to quit"
  read doit
    case $doit in
      y|Y)
        # finding os name
        # https://stackoverflow.com/a/39959192
        distro=$(awk -F'=' '/^ID=/ {print tolower($2)}' /etc/*-release 2> /dev/null)
        # echo $distro

          # finding distribuition name and assigning package manager based on it
          case $distro in
            ubuntu | raspbian | debian)
              package_manager="sudo apt-get install -y"
              echo $package_manager
              ;;

            arch | manjaro)
              package_manager="sudo pacman -S --noconfirm"
              echo $package_manager
              ;;

            alpine)
              package_manager="sudo apk --update add"
              echo $package_manager
              ;;

            centos)
              package_manager="sudo yum install -y"
              echo $package_manager
              ;;

            fedora)
              package_manager="sudo dnf install -y"
              echo $package_manager
              ;;

            *)
              echo "Can't find distro name. Exiting installer"
              exit 0
              ;;
          esac

        # installing tar via $package_manager
        ${package_manager} tar
        # note: you can use this 2 var method to install multiple packages
        # package="tar"
        # ${package_manager} ${package}
        ;;

      q|Q)
        echo "qutting"; exit 0
        ;;

      *)
        echo "Invalid Option"; exit 1
        ;;
    esac
fi


# echo "Finding out cpu architecture and downloading relevant package"

cpuArch=$(uname -m)
# echo "$cpuArch"

# assigning wget with options and url to var
mkdir /home/$USER/.config/adguard
downloader="wget --no-check-certificate --content-disposition -P /home/$USER/.config/adguard/ https://github.com/AdguardTeam/AdGuardHome/releases/latest/download/"

# downloading relevant package based on architecture
# https://gist.github.com/jwebcat/5122366
case $cpuArch in
  x86_64)
    ${downloader}AdGuardHome_linux_amd64.tar.gz
    ;;

  aarch64 | arm64 | armv8)
    ${downloader}AdGuardHome_linux_arm64.tar.gz
    ;;

  armv7)
    ${downloader}AdGuardHome_linux_armv7.tar.gz
    ;;

  armv6l | armv6)
    ${downloader}AdGuardHome_linux_armv6.tar.gz
    ;;
esac

# extracting tarball
tar -C /home/$USER/.config/adguard -zxvf /home/$USER/.config/adguard/AdGuardHome*

echo "\nInstalling Adguard\n"
sudo /home/$USER/.config/adguard/AdGuardHome/AdGuardHome -s install
unset doit distro package_manager cpuArch
```

#### Uninstallation script

```bash title="uninstall.sh"
#!/bin/sh
echo "Uninstalling Adguard if installed via my script\n"
sudo /home/$USER/.config/adguard/AdGuardHome/AdGuardHome -s uninstall

echo "Enter M to move ~/.config/adguard to adgaurd-backup or D to delete the directory"
read userInput
case $userInput in
  M|m)
    echo "Moving adguard to adgaurd-backup"
    mv /home/$USER/.config/adguard /home/$USER/.config/adguard-backup
    unset userInput
    exit 0;
    ;;

  D|d)
    echo "Removing Adguard dir from .config"
    sudo rm -rf /home/$USER/.config/adguard
    unset userInput
    exit 0;
    ;;

  *)
    echo "Invalid input. Exiting with code 1"
    unset userInput
    exit 1;
    ;;
esac
```

## Docker

### Basic info

!!! base-info "Basic info with official links"

    - [x] Web-GUI Port: `80`
    - [x] Other Ports: `53` `67` `68` `443` `3000` `853` `784` `8853` `5443`
    - [x] Image: [DockerHub](https://hub.docker.com/r/adguard/adguardhome "Adguard image on Docker Hub"){:target="_blank" rel="noopener noreferrer"}
    - [x] Repo: [Github](https://github.com/AdguardTeam/AdGuardHome "Adguard Github repo"){:target="_blank" rel="noopener noreferrer"}
    - [x] Website: [Official Adguard Wiki](https://github.com/AdguardTeam/AdGuardHome/wiki "Adguard official Docs"){:target="_blank" rel="noopener noreferrer"}

### docker-compose.yml

```yaml
---
version: '3.3'
services:
  adguard-home:
    image: adguard/adguardhome:latest
    container_name: adguard
    networks:
      - proxy # rename this to your custom docker network
    restart: unless-stopped
    volumes:
      - "${CFG_DIR}/adguard/work:/opt/adguardhome/work"
      - "${CFG_DIR}/adguard/conf:/opt/adguardhome/conf"
    ports:
      - 192.168.29.12:53:53/tcp
      - 192.168.29.12:53:53/udp
      - 67:67/udp
      - 68:68/udp
      - 80:80/tcp
      - 443:443/tcp
      - 443:443/udp
      - 3000:3000/tcp
      - 853:853/tcp
      - 784:784/udp
      - 853:853/udp
      - 8853:8853/udp
      - 5443:5443/tcp
      - 5443:5443/udp
    cap_add:
      - NET_ADMIN

networks:
  proxy:    # rename this to your custom docker network.
    external: true
```

### deploy.sh

```bash
mkdir -p $CFG_DIR/adguard/{work,conf}
docker compose up -d
```