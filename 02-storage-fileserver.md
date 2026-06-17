````markdown
# 02 - Storage Architecture & Data Migration

## Overview

This document describes the storage architecture used in the homelab and the migration process from legacy filesystems to a Linux-native ext4 storage layout.

The objective was to establish a maintainable and reliable storage platform while preserving existing data throughout the migration.

---

## Objectives

- Centralize all personal data on a dedicated storage volume.
- Replace legacy HFS+ storage with ext4.
- Preserve existing data during migration.
- Simplify future backups.
- Separate operating system and data storage.

---

## Physical Storage Layout

| Device | Purpose | Filesystem |
|----------|----------|------------|
| Internal SSD | Proxmox operating system and LXC root disks | ext4 / LVM |
| 4 TB External HDD | Primary data storage | ext4 |
| 2 TB External HDD | Backup and migration storage | ext4 |
| 500 GB External HDD | Reserved for future backup use | Planned |

---

## Final Storage Architecture

```text
Proxmox Host
│
├── Internal SSD
│   ├── Proxmox VE
│   ├── LXC Containers
│   └── Docker host storage
│
├── /mnt/data
│   └── Primary Storage
│       ├── Archive
│       ├── Documents
│       ├── Music
│       ├── Other
│       ├── Photos
│       └── Services
│
└── /mnt/backup
    └── Backup Storage
```

---

## Initial Situation

The primary external storage device originally used Apple's HFS+ filesystem.

Although Linux can mount HFS+ volumes, write support is limited and long-term production use is not recommended.

A migration to the native Linux ext4 filesystem was therefore planned.

---

## Migration Strategy

To minimize risk, the migration followed a staged process.

### Step 1 – Mount the source disk

The legacy HFS+ disk was mounted in read-only mode.

```bash
mount -t hfsplus /dev/sdX /mnt/source
```

---

### Step 2 – Create a temporary backup

All existing data was copied to a dedicated migration disk.

```bash
rsync -aHAX --info=progress2 /mnt/source/ /mnt/backup/migration/
```

---

### Step 3 – Verify copied data

Verification included:

- directory structure
- file count
- storage usage
- spot checks of random files

Example:

```bash
du -sh /mnt/backup/migration
```

---

### Step 4 – Repartition the original disk

A new GPT partition table was created.

```bash
parted /dev/sdX mklabel gpt
```

The disk was repartitioned as a single Linux partition.

---

### Step 5 – Format using ext4

```bash
mkfs.ext4 -L DATA /dev/sdX1
```

---

### Step 6 – Mount the new filesystem

```bash
mount /dev/sdX1 /mnt/data
```

Persistent mounting was configured using filesystem UUIDs in `/etc/fstab`.

---

### Step 7 – Restore the data

The previously migrated data was copied back onto the new ext4 filesystem.

```bash
rsync -aHAX --info=progress2 /mnt/backup/migration/ /mnt/data/
```

---

### Step 8 – Validate the migration

Final verification included:

- total storage usage
- directory structure
- file accessibility
- network share functionality

Only after successful validation was the migration considered complete.

---

## Directory Structure

The production storage follows a simple top-level organization.

```text
/mnt/data

├── Archive
├── Documents
├── Music
├── Other
├── Photos
└── Services
```

This separation keeps user data and self-hosted application data logically organized.

---

## Integration with LXC Containers

Rather than exposing physical disks directly to containers, Proxmox bind mounts are used.

Example:

```text
Host

/mnt/data

↓

Fileserver LXC

/mnt/files
```

Advantages include:

- simplified administration
- persistent storage independent of containers
- easier migrations
- easier backups
- improved portability

---

## Backup Strategy

The storage layout was designed to support a 3-2-1 backup philosophy.

### Primary copy

- Production storage

### Secondary copy

- Dedicated backup drive

### Third copy

- Additional offline or manually maintained backup

This approach reduces the risk of accidental data loss while simplifying recovery procedures.

---

## Lessons Learned

### Never migrate without a verified backup

Data should exist in at least two independent locations before repartitioning or formatting any storage device.

---

### Verify before deleting

Original media should only be erased after:

- migration completed successfully
- copied data verified
- storage integrity confirmed
- network access tested

---

### Prefer native Linux filesystems

Using ext4 instead of HFS+ provides:

- better compatibility
- improved permissions handling
- simplified maintenance
- higher long-term reliability

---

### Use UUIDs instead of device names

Filesystem UUIDs remain stable across reboots, whereas device names such as `/dev/sdb1` may change.

---

### Separate infrastructure from data

Persistent storage should remain outside container filesystems.

Using bind mounts keeps services portable and allows containers to be recreated without risking application data.

---

### Plan storage before deploying services

Establishing a stable storage architecture before deploying applications significantly reduces future maintenance complexity.

---

## Current Status

The migration was completed successfully.

The current storage environment provides:

- ✅ ext4 primary storage
- ✅ centralized network file access
- ✅ bind-mounted storage for LXC containers
- ✅ dedicated backup storage
- ✅ scalable foundation for future self-hosted services
````
