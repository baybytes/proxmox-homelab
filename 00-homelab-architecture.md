# 07 - Homelab Architecture

## Overview

This document provides an overview of the current homelab architecture, including the virtualization platform, storage layout, networking, and deployed services.

The environment is designed to provide a lightweight, low-power infrastructure for learning, self-hosting and practical systems administration while remaining easy to maintain and expand.

---

## Design Principles

The overall architecture follows several key principles:

* Separate infrastructure from application data.
* Keep services isolated using LXC containers.
* Store persistent data outside container filesystems.
* Minimize resource consumption.
* Build around reproducible and maintainable configurations.
* Design for future scalability.

---

## High-Level Architecture

```text
                           Internet
                                │
                         Home Router
                                │
                    ┌────────────────────┐
                    │   Proxmox Host      │
                    └─────────┬───────────┘
                              │
         ┌────────────────────┼────────────────────┐
         │                    │                    │
         │                    │                    │
     Pi-hole LXC        Fileserver LXC      Joplin LXC
         │                    │                    │
         │                 Samba SMB          Docker Compose
         │                    │                    │
         │                    │             ┌──────┴──────┐
         │                    │             │             │
         │                    │      Joplin Server   PostgreSQL
         │                    │
         └────────────────────┴───────────────────────────────
                              │
                        Host Storage
                           /mnt/data
```

---

## Virtualization Layer

The homelab is built on Proxmox VE using Linux Containers (LXC) for lightweight workloads.

LXC containers provide:

* low resource consumption
* fast startup times
* simplified management
* efficient isolation

Future workloads requiring complete operating system isolation may be deployed as virtual machines.

---

## Storage Architecture

Persistent storage is managed centrally by the Proxmox host.

Rather than storing application data inside containers, bind mounts expose host directories to individual services.

```text
Proxmox Host host-managed storage
│
▼
LXC Container mounted service data
```

This approach keeps storage independent from the lifecycle of individual containers.

---

## Network Services

The environment currently provides several internal services.

| Service       | Purpose              |
| ------------- | -------------------- |
| Pi-hole       | DNS filtering        |
| Samba         | Central file sharing |
| Joplin Server | Note synchronization |
| PostgreSQL    | Database backend     |

Each service operates independently within its own containerized environment.

---

## Client Integration

Supported client platforms currently include:

* macOS
* Windows
* iPhone / iPad

Clients access centralized services through the local network using standard protocols such as SMB and HTTP.

---


## Data Organization

The primary storage follows a simple hierarchical structure.

```text
Host-managed storage
├── Shared
├── Media
├── Services
├── Backups
└── Archive

User-facing data and application data remain logically separated while sharing the same underlying storage infrastructure.
```

Personal data and application data remain logically separated while sharing the same underlying storage infrastructure.

---

## Scalability

The architecture was intentionally designed for future expansion.

Planned additions include:

* Home Assistant
* Paperless-ngx
* Immich
* automated backups
* remote access via VPN
* monitoring and alerting

The existing storage and virtualization model allows these services to be integrated without major redesign.

---

## Design Decisions

Several architectural choices were made to improve long-term maintainability:

* use of unprivileged LXC containers where possible
* centralized host-managed storage
* bind-mounted persistent volumes
* separation of infrastructure and application data
* independent service deployment
* minimal inter-service dependencies

These decisions reduce operational complexity and simplify recovery procedures.

---

## Lessons Learned

* Planning storage before deploying applications significantly reduces future migration effort.
* Separating persistent data from compute resources improves resilience.
* Lightweight LXC containers are well suited for infrastructure services.
* Simple architectures are easier to troubleshoot and maintain.
* Incremental expansion allows infrastructure to evolve without unnecessary complexity.

---

## Current Status

The current homelab successfully provides:

* Proxmox VE virtualization
* Centralized storage architecture
* Network-wide DNS filtering
* SMB file sharing
* Self-hosted Joplin synchronization
* Persistent bind-mounted application storage

The platform now serves as a stable foundation for future self-hosted infrastructure projects and continued learning in Linux systems administration and virtualization.
