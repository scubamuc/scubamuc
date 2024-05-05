<h1 align="center">SCUBA's self hosted Nextcloud snap server</h1>

<p align="center" width="100%">
    <img width="33%" src="https://avatars.githubusercontent.com/u/54933878?s=400&u=31132eb8a567528f005143a0d339174848a06df8&v=4" alt="scubamuc">
</p>

for a self hosted [Nextcloud](https://nextcloud.com/) I turned to [Ubuntu Server](https://ubuntu.com/download/server) and [LXD](https://canonical.com/lxd) to create an [LXC container](https://documentation.ubuntu.com/lxd/en/latest/explanation/lxd_lxc/) for [Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap). There is a great community behind Nextcloud snap and support is fantastic. The greatness of Linux together with supported hardware enables anyone to run a 24/7 Nextcloud server. 

My goal is a simple safe and reliable setup with ample resources for 5+ family users, affordable, efficient and low maintenence. My day job is software support for a leading WMS. I'm an avid scuba diver and enjoy Linux and FOSS. I'm a dad and husband thus I'm not keen on spending much of my free time doing server-maintenence.

**Quicklinks**
* [Official Nextcloud snap wiki](https://github.com/nextcloud-snap/nextcloud-snap/wiki)
* [SCUBA's Nextcloud snap wiki docs](https://github.com/scubamuc/scubamuc.github.io/tree/scubamuc/wiki-md)
* [SCUBA's bash scripts](https://github.com/scubamuc/scubamuc.github.io/tree/scubamuc/bash-scripts)

---
# 1. System specs

## 1.1 Hardware
**Requirements:**
* Affordable, efficient, small 
* Small [energy footprint](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/SYSTEM--calculate_power_consumption.md)
* Linux compatible
* Ample resources  - (_5+ users_)
* Low maintenence - (_uptime 24/7_)
* Redundancy - (_failover, cold-standby & backup_)
* Multiple services - (_Nextcloud, Webserver, FW, VPN, DNS, Proxy_)

----
>**2x Lenovo ThinkCentre M92p Tiny (_refurbished_) - stackable 1xServer & 1xBackup** 
>  * CPU: Intel Core i5-3470T
>  * PSU: 16W/12W load/idle
>  * RAM: 16GB
>  * SSD: 500GB
> <img src="https://github.com/scubamuc/scubamuc.github.io/assets/54933878/da9e2da8-e00f-41d0-a207-c340f42743e3" width="400">
----

## 1.2 OS & Software
**Requirements:**
* [Ubuntu Server](https://ubuntu.com/download/server) (_easy setup & maintenence, extended security maintenece; [Ubuntu Pro, ESM](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/SYSTEM--Ubuntu-pro.md) or [unattended updates & updgrades](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/SYSTEM--Unattended_Upgrades.md)_)
* [Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap) (_[easy setup & automated updates](https://github.com/nextcloud-snap/nextcloud-snap/wiki/install-Nextcloud-snap)_)
* [LXD](https://documentation.ubuntu.com/lxd/en/latest/getting_started/) (_multiple LXC-containers_)
   * [LXDMosaic](https://github.com/turtle0x1/LxdMosaic#-lxdmosaic) LXD-server management GUI
* Packages:
 `cockpit, htop, lnav, mc, openssh-server, sysstat, tmux`
* Snaps: 
 `lxd, lxdmosaic, nextcloud`

### LXD-server setup
* [Bridged network](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/LXD-LXC-bridged-network.md) (_IP from DHCP_)
* Multiple LXC containers (_Nextcloud, Webserver, FW, VPN, DNS, Proxy_)
* automatic rotating snapshots (_daily_) **0 downtime 24/7**
* automatic [synchronisation to LXD-backup-server](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/LXD-LXC-copy_container_to_backup_server.md) (_weekly_)
* automatic rotating backup to NAS (_monthly_)

### LXD-backup-server setup
* Identical setup to LXD-server -- cold-standby
* [Bridged network](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/LXD-LXC-bridged-network.md) (_IP from DHCP_)
* Multiple [*synchronised*](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/LXD-LXC-copy_container_to_backup_server.md) LXC containers (_Nextcloud, Webserver, FW, VPN, DNS, Proxy_)
* WOL `ctr-wake` (_weekly_) [container synchronisation](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/LXD-LXC-copy_container_to_backup_server.md)
* WOL (_manual_) failover

----
# 2. Procedure

## 2.1 Install Nextcloud-snap in LXC container on LXD server

* Prepare [LXD server](https://documentation.ubuntu.com/lxd/en/latest/) with [Ubuntu Server](https://ubuntu.com/download/server)  and [bridged network](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/LXD-LXC-bridged-network.md)
* Prepare [LXC container](https://documentation.ubuntu.com/lxd/en/latest/getting_started/) with [Ubuntu Server](https://ubuntu.com/download/server)
* Install [Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap) in LXC-container
* Set [FQDN](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/NEXTCLOUD.snap--set_FQDN.md) in `/etc/hosts` in LXC-container
* Nextcloud snap handles [Lets-Encrypt certificate](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/NEXTCLOUD.snap--LetsEncrypt_wiki.md#enable-letsencrypt-in-nextcloud-snap) automatically with `nextcloud.enable-https lets-encrypt` 
    * *optionally* handle certificates with [reverse Proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) or [NGINX-Proxy-Manager](https://github.com/nextcloud-snap/nextcloud-snap/wiki/NGINX-proxy-manager)
* [Configure Nextcloud](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap) and regain control over your data

----
## 2.2 LXC container snapshot / backup & restore

#### Backup LXC container with snapshot/image & restore in shell [scripted](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/bash-scripts/LXC-ContainerBackup.sh) or GUI [LXDMosaic](https://github.com/turtle0x1/LxdMosaic#-lxdmosaic)

![grafik](https://user-images.githubusercontent.com/54933878/216815478-9a58192c-b769-487d-b781-09593a4ec2c9.png)

* LXC container snapshot [scripted](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/bash-scripts/LXC-ContainerBackup.sh) or GUI [LXDMosaic](https://github.com/turtle0x1/LxdMosaic#-lxdmosaic)
* LXC container image from snapshot [scripted](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/bash-scripts/LXC-ContainerBackup.sh) or GUI [LXDMosaic](https://github.com/turtle0x1/LxdMosaic#-lxdmosaic)
* LXC image copy/move to wherever
* LXC image rotate [scripted](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/bash-scripts/LXC-ContainerBackup.sh) or GUI [LXDMosaic](https://github.com/turtle0x1/LxdMosaic#-lxdmosaic)

This works fine as [scripted](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/bash-scripts/LXC-ContainerBackup.sh) cronjob or [LXDMosaic](https://github.com/turtle0x1/LxdMosaic#-lxdmosaic) schedule. 0 downtime.

[rotating LXC container snapshot script](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/bash-scripts/LXC-ContainerBackup.sh)

#### Restore LXC container in shell or GUI [LXDMosaic](https://github.com/turtle0x1/LxdMosaic#-lxdmosaic)

* `lxc image import $PATH/to/$IMAGENAME.tar.gz --alias $IMAGENAME`
* `lxc launch $IMAGENAME $CONTAINERNAME`

----

