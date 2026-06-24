# 02 - Storage Architecture & Data Migration

## Overview

This document describes the storage architecture implemented in the homelab environment and the migration from legacy filesystems to a Linux-native storage layout.

The primary objective was to establish a reliable, maintainable and scalable storage platform for self-hosted services while preserving all existing data throughout the migration process.

The resulting design separates operating system components from persistent application data and provides a solid foundation for centralized file storage, containerized services and future backup automation.

---

## Objectives

The storage architecture was designed with the following goals in mind:

* Create a centralized location for user-facing and service-related data.
* Replace legacy HFS+ storage with the Linux-native ext4 filesystem.
* Perform the migration without data loss through staged verification.
* Keep persistent application data independent from LXC root filesystems.
* Simplify future maintenance, backups and disaster recovery.
* Establish a scalable storage layout that can support additional self-hosted services over time.

---

## Physical Storage Layout

| Device              | Purpose                                        | Filesystem |
| ------------------- | ---------------------------------------------- | ---------- |
| Internal SSD        | Proxmox VE operating system and LXC root disks | ext4 / LVM |
| External HDD        | Primary production storage                     | ext4       |
| External HDD        | Backup and temporary migration storage         | ext4       |
| External HDD          Reserved for future backup purposes            | Planned    |


---

## Migration Process

The primary storage device originally used Apple's HFS+ filesystem.

To migrate safely to ext4, all data was first copied to temporary backup storage using `rsync`.

Only after verifying the migrated data was the original disk repartitioned, formatted and restored.

Key operations included:

```bash
rsync -aHAX --info=progress2 <source> <destination>
mkfs.ext4
parted
mount
```

The migration was verified by comparing directory structures, storage usage and file accessibility before returning the system to production.

---

## Directory Structure

```text
Host-managed storage
├── Shared
├── Media
├── Services
├── Backups
└── Archive

---

## Integration with LXC Containers


Persistent storage is mounted on the Proxmox host and exposed to containers through bind mounts.


Host
host-managed storage

↓ LXC Container
mounted service data

This approach separates infrastructure from application data and simplifies future migrations.

---

## Backup Strategy

The storage architecture was designed around a 3-2-1 backup philosophy.

| Copy | Location |
|--------|----------|
| Primary | Production storage |
| Secondary | Dedicated backup drive |
| Third | Offline / manual backup |


---

## Lessons Learned

- Always verify backups before formatting disks.
- Prefer native Linux filesystems for production workloads.
- Store persistent data outside container root filesystems.
- Use UUIDs instead of device names for persistent mounts.
- Plan storage architecture before deploying services.
- Validate migrations before deleting original data.

---

## Current Status

The storage migration has been completed successfully.

Current environment:

- ext4 production storage
- centralized SMB access
- bind-mounted LXC storage
- backup storage available
- prepared for future self-hosted services
