# 05 - Self-Hosted Joplin Server

## Overview

This document describes the deployment of a self-hosted Joplin Server running inside an unprivileged Debian LXC container on Proxmox VE.

The objective was to replace third-party cloud synchronization with a private, self-hosted solution while maintaining synchronization across macOS, Windows and iOS devices.

---

## Architecture

```
                    Proxmox VE
                         │
                ┌─────────────────┐
                │   LXC 103        │
                │    Debian        │
                └────────┬─────────┘
                         │
                     Docker Engine
                         │
              ┌──────────┴──────────┐
              │                     │
      Joplin Server          PostgreSQL
              │                     │
              └──────────┬──────────┘
                         │
               Bind Mount (/mnt/joplin)
                         │
              Host Storage (/mnt/data)
```

---

## Objectives

- Self-host Joplin synchronization
- Keep persistent data outside the container filesystem
- Use PostgreSQL as backend database
- Enable synchronization between multiple devices
- Simplify future backup and disaster recovery

---

## Container Configuration

| Setting | Value |
|----------|-------|
| Platform | Proxmox VE |
| Container Type | Unprivileged LXC |
| Operating System | Debian 12 |
| CPU | 1 Core |
| Memory | 1024 MB |
| Swap | 512 MB |
| Root Disk | 10 GB |

Persistent application data is stored outside the container using a Proxmox bind mount.

```
Host
└── /mnt/data/Services/Joplin

↓

Container
└── /mnt/joplin
```

---

## Docker Stack

The deployment consists of two services:

- PostgreSQL
- Joplin Server

Docker Compose is used for orchestration.

Persistent PostgreSQL data is stored inside the mounted storage directory rather than the container filesystem.

---

## Network

The service is exposed on:

```
http://<server-ip>:22300
```

Clients connect directly to the Joplin Server using their own user accounts.

---

## Synchronization Workflow

### Initial Migration

An existing local Joplin installation on macOS contained the primary note database.

Migration process:

1. Configure synchronization target to Joplin Server.
2. Authenticate with server credentials.
3. Upload all local notebooks and resources.
4. Connect additional devices.
5. Download synchronized data from the server.

The first synchronization uploads all existing notes before additional clients can retrieve them.

---

## User Management

The deployment separates administration from daily use.

### Administrative account

- Server administration only

### Personal account

- Desktop synchronization
- Mobile synchronization
- Future Windows synchronization

This approach follows the principle of least privilege.

---

## Persistent Storage

Application data is intentionally stored outside the LXC root filesystem.

Benefits include:

- simplified backups
- easier migration
- container replacement without data loss
- host-level storage management

---

## Troubleshooting

### PostgreSQL container repeatedly restarted

#### Symptoms

The PostgreSQL container continuously exited and restarted.

```
Restarting (1)
```

#### Root Cause

The `POSTGRES_PASSWORD` environment variable was not configured correctly.

As a result, PostgreSQL refused to initialize the database.

#### Resolution

- Corrected the environment variables.
- Recreated the database volume.
- Restarted the Docker Compose stack.

```
docker compose down
rm -rf postgres
docker compose up -d
```

Result:

- PostgreSQL initialized successfully.
- Joplin Server started normally.

---

### Mobile device initially displayed no notes

#### Symptoms

The mobile client authenticated successfully but displayed an empty notebook list.

#### Root Cause

The initial synchronization from the desktop client had not yet uploaded the existing local database to the server.

#### Resolution

Allow the desktop client to complete the first upload.

Afterwards the mobile client downloaded all notebooks and attachments successfully.

---

## Lessons Learned

- Persistent application data should be stored outside container root filesystems.
- Docker Compose simplifies deployment and future upgrades.
- Environment variables should always be validated before deployment.
- Initial synchronization behaviour differs from ongoing synchronization.
- Separating administrator and daily user accounts improves operational security.
- Self-hosted synchronization can fully replace cloud-based note storage while maintaining cross-platform compatibility.

---

## Current Status

The deployment has been successfully validated with:

- ✅ macOS
- ✅ iPhone / iPad

Synchronization works reliably across devices using the self-hosted infrastructure.

Future clients can be added by configuring the same synchronization endpoint and authenticating with the appropriate user account.
