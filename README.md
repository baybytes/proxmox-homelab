# Proxmox Homelab

A self-hosted infrastructure project built on **Proxmox VE** with a focus on Linux system administration, virtualization, storage management, networking and containerized services.

The project serves both as a personal production environment and as a practical learning platform for modern infrastructure technologies.

---

## Objectives

The homelab was designed to:

* Build practical experience with Linux and Proxmox VE.
* Deploy lightweight services using unprivileged LXC containers.
* Centralize personal data and self-hosted applications.
* Implement a maintainable storage and backup architecture.
* Gain hands-on experience with Docker and PostgreSQL.
* Replace third-party cloud services with self-hosted alternatives.

---

## Technology Stack

| Category          | Technology             |
| ----------------- | ---------------------- |
| Hypervisor        | Proxmox VE             |
| Virtualization    | Linux Containers (LXC) |
| Container Runtime | Docker                 |
| Database          | PostgreSQL             |
| DNS               | Pi-hole                |
| File Sharing      | Samba (SMB)            |
| Notes             | Joplin Server          |
| Filesystem        | ext4                   |
| Operating System  | Debian                 |

---

## Current Infrastructure

| Service       | Description                      |
| ------------- | -------------------------------- |
| Pi-hole       | Network-wide DNS filtering       |
| Fileserver    | Central SMB file storage         |
| Joplin Server | Self-hosted note synchronization |
| PostgreSQL    | Backend database for Joplin      |

---

## Storage Architecture

```text
                 Proxmox Host
                      │
        ┌─────────────┴─────────────┐
        │                           │
   Internal SSD                External Storage
        │                           │
   Proxmox + LXC                /mnt/data
                                    │
        ┌───────────────────────────┼──────────────────────────┐
        │            │             │             │             │
     Archive     Documents      Photos       Services      Music
```

Persistent application data is stored outside container filesystems using Proxmox bind mounts, simplifying backups, migrations and disaster recovery.

---

## Implemented Features

* ✅ Proxmox VE virtualization platform
* ✅ Unprivileged Debian LXC containers
* ✅ Centralized SMB fileserver
* ✅ Self-hosted Joplin Server
* ✅ Docker Compose deployments
* ✅ PostgreSQL integration
* ✅ ext4 storage migration
* ✅ Multi-device synchronization
* ✅ Cross-platform SMB compatibility (macOS, Windows, iOS)
* ✅ Backup-oriented storage design

---

## Documentation

Detailed project documentation is available in the `main/` directory.

| Document                     | Description                  |
| ---------------------------- | ---------------------------- |
| `01-proxmox-setup.md`        | Initial Proxmox deployment   |
| `02-storage-architecture.md` | Storage layout and migration |
| `03-backup-strategy.md`      | Backup philosophy            |
| `04-samba-fileserver.md`     | SMB fileserver configuration |
| `05-joplin-server.md`        | Joplin Server deployment     |
| `06-pihole-dns-server.md`    | DNS infrastructure           |
| `00-homelab-architecture.md` | Overall system architecture  |

---

## Future Roadmap

Planned additions include:

* Home Assistant
* Paperless-ngx
* Immich
* Automated backup workflows
* Remote access via VPN
* Infrastructure monitoring

---

## Learning Outcomes

This project demonstrates practical experience with:

* Linux system administration
* Proxmox virtualization
* LXC container management
* Docker and Docker Compose
* Storage planning and filesystem migration
* Samba administration
* PostgreSQL deployment
* Backup strategy design
* Network services
* Self-hosted infrastructure
