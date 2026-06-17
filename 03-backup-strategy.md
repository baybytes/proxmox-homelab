# 03 - Backup Strategy

## Overview

This document describes the backup philosophy and disaster recovery strategy implemented in the homelab environment.

The objective is to minimize the risk of data loss while keeping backup procedures simple, reproducible and maintainable.

The storage architecture was designed from the beginning with backup and recovery in mind rather than treating backups as an afterthought.

---

## Design Goals

The backup strategy is based on the following principles:

* Protect personal and application data against hardware failure.
* Separate production data from backup storage.
* Keep services independent from container filesystems.
* Simplify restoration procedures.
* Enable future automation without redesigning the storage architecture.

---

## Storage Separation

Persistent data is intentionally stored outside LXC containers.

Application data resides on host-mounted storage volumes which are exposed to containers using bind mounts.

```text
                 Proxmox Host

                    /mnt/data
                         │
         ┌───────────────┴───────────────┐
         │                               │
     Personal Data              Application Data
                                         │
                                  Services/Joplin
                                  Services/...
```

This design ensures that containers can be recreated without affecting persistent data.

---

## Backup Philosophy

The environment follows the widely accepted **3-2-1 backup principle**.

| Copy      | Purpose                    |
| --------- | -------------------------- |
| Primary   | Production storage         |
| Secondary | Dedicated backup drive     |
| Tertiary  | Offline or external backup |

By maintaining multiple independent copies, accidental deletion or hardware failure becomes significantly less critical.

---

## Current Backup Layout

| Storage         | Function              |
| --------------- | --------------------- |
| Primary storage | Live production data  |
| Backup drive    | Secondary backup copy |
| External backup | Planned               |

The dedicated backup drive is also used temporarily during storage migrations before destructive operations are performed.

---

## Disaster Recovery Considerations

The infrastructure separates services from data.

For example:

```text
LXC Container
      │
      ▼
Destroyed or recreated

Persistent Storage

      │

      ▼

Remains unchanged
```

In case of container failure, rebuilding the operating system and reconnecting existing storage is significantly easier than recovering application data from inside the container itself.

---

## Future Improvements

The current architecture provides a solid foundation for future enhancements.

Planned improvements include:

* automated scheduled backups
* incremental snapshots
* off-site synchronization
* backup verification jobs
* notification and monitoring

These features can be added without restructuring the existing storage layout.

---

## Lessons Learned

* Backup planning should begin before deploying production services.
* Persistent application data should never depend on container root filesystems.
* Every migration should include at least one verified backup before formatting or repartitioning storage.
* Separating infrastructure from data simplifies recovery and maintenance.
* A simple and well-tested backup strategy is more valuable than a complex solution that is never verified.

---

## Current Status

The current backup architecture provides:

* ✅ dedicated production storage
* ✅ separate backup storage
* ✅ bind-mounted persistent application data
* ✅ migration-safe storage layout
* ✅ foundation for future backup automation

The overall design prioritizes maintainability, recoverability and long-term operational stability.
