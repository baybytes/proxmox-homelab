# 01 - Proxmox Setup

## Overview

This document describes the initial Proxmox VE setup used for my personal homelab environment.

The goal of this setup is to provide a lightweight, low-power virtualization platform for self-hosted services, storage, backups, and infrastructure experiments.

## Hardware

| Component        | Details                                           |
| ---------------- | ------------------------------------------------- |
| Host             | Thin Client                                       |
| CPU              | AMD Ryzen Embedded R1505G                         |
| Cores            | 4 logical cores                                   |
| System Disk      | 256 GB Samsung SSD                                |
| External Storage | 4 TB WD Elements, 2 TB Seagate HDD, 500 GB WD HDD |
| Network          | Home LAN via Router                           |


## Hypervisor

| Component           | Details                        |
| ------------------- | ------------------------------ |
| Platform            | Proxmox VE                     |
| Kernel              | Linux 6.17.2-1-pve             |
| Manager Version     | pve-manager 9.1.1              |
| Boot Mode           | EFI                            |
| Virtualization Type | LXC containers and planned VMs |

## Initial Goals

The Proxmox host was set up to run several lightweight self-hosted services:

* Pi-hole for network-wide DNS filtering
* Samba fileserver for central SMB storage
* Joplin Server for self-hosted note synchronization
* Future services such as Home Assistant, Paperless-ngx, and backup automation

## Network Design

The host and services use fixed or reserved IP addresses in the local network.

| System         |       IP Address | Purpose           |
| -------------- | ---------------: | ----------------- |
| Proxmox Host   | `1****` | Hypervisor        |
| Pi-hole LXC    | `1****` | DNS / ad blocking |
| Fileserver LXC | `1****` | SMB file server   |
| Joplin LXC     | `1****` | Joplin Server     |

DHCP reservations are preferred over manually configured static IPs inside containers. This keeps network management centralized in the router and reduces the risk of IP conflicts.

## Storage Layout

The host uses an internal SSD for Proxmox and container disks. External drives are mounted on the Proxmox host and then passed into containers via bind mounts.

| Drive            | Mount Point   | Purpose                           |
| ---------------- | ------------- | --------------------------------- |
| Samsung SSD      | `/`           | Proxmox system and LXC root disks |
| 4 TB WD          | `/mnt/data`   | Main fileserver data              |
| 2 TB Seagate     | `/mnt/backup` | Backup and migration storage      |
| 500 GB WD HDD    | pending       | Secondary backup / future use     |

## Design Decisions

### LXC Containers

LXC containers are used for lightweight services because they consume fewer resources than full virtual machines.

Used for:

* Pi-hole
* Samba fileserver
* Joplin Server

### Virtual Machines

VMs are planned for workloads where full OS isolation or better appliance compatibility is useful, such as Home Assistant OS.

### Bind Mounts

External storage is mounted on the Proxmox host and passed into containers using bind mounts.


This keeps physical storage management on the host while allowing containers to access only the directories they need.

## Lessons Learned

* Always verify disks with `lsblk` before formatting or mounting.
* Use UUIDs in `/etc/fstab` instead of device names like `/dev/sdb1`.
* USB drives may need additional boot-time handling because they can initialize slowly.
* Unprivileged LXC containers require UID/GID mapping awareness.
* Bind mount permissions often need to be fixed on the Proxmox host, not inside the container.
* DHCP reservations are cleaner than hardcoding IPs in every container.

## Useful Commands

List block devices:

```bash
lsblk -o NAME,SIZE,MODEL,SERIAL,FSTYPE,MOUNTPOINT
```

Check mounted filesystems:

```bash
df -h
```

Check container list:

```bash
pct list
```

Enter a container:

```bash
pct enter <CTID>
```

View LXC configuration:

```bash
cat /etc/pve/lxc/<CTID>.conf
```

Restart a container:

```bash
pct restart <CTID>
```

## Current LXC Containers

| CT ID | Hostname     | Purpose                                  |
| ----: | ------------ | ---------------------------------------- |
|   101 | `pihole`     | DNS filtering                            |
|   102 | `fileserver` | Samba SMB fileserver                     |
|   103 | `joplin`     | Joplin Server with Docker and PostgreSQL |

## Status

The Proxmox base setup is complete.

Completed milestones:

* Proxmox host installed and updated
* External storage detected and validated
* 4 TB WD Elements migrated to ext4
* Samba fileserver running
* Joplin Server running in LXC with Docker Compose
* Mac and mobile Joplin clients synchronizing successfully

## Next Steps

Planned follow-up work:

* Automate backups for macOS and Windows clients
* Add Home Assistant as a VM
* Document Pi-hole setup
* Document Samba fileserver setup
* Document Joplin Server deployment
* Add architecture diagrams
