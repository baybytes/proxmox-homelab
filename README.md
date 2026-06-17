# proxmox-homelab

docs/
│   ├── 01-proxmox-setup.md
│   ├── 02-fileserver.md
│   ├── 03-storage-migration.md
│   ├── 04-samba.md
│   ├── 05-joplin-server.md
│   ├── 06-backup-strategy.md
│   └── architecture.md

Persönliches Homelab auf Basis eines HP Thin Clients mit Fokus auf:

- Virtualisierung mit Proxmox VE
- Debian LXC Container
- Docker Workloads
- zentralem Fileserver
- Self-Hosted Services
- Backup-Strategien
- Systemadministration

## Infrastruktur

- Proxmox VE
- Pi-hole
- Samba Fileserver
- Joplin Server
- PostgreSQL
- Docker

## Storage

- 4 TB WD Elements (ext4)
- 2 TB Seagate Backup
- SSD für Proxmox Host

## Features

- zentrale Dateifreigaben
- Multi-Device Joplin Synchronisation
- Backup-Konzept
- LXC Containerisierung
- Docker Compose Deployments

## Dokumentation

Siehe `docs/`.
