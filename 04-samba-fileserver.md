# 04 - Samba Fileserver

## Overview

This document describes the Samba SMB fileserver deployment used in the Proxmox homelab.

The primary goal is to provide centralized network storage that can be accessed from Linux, macOS, Windows and iOS devices while maintaining a simple and maintainable architecture.

---

## Architecture

```
                 Proxmox Host
                      │
              /mnt/data (ext4)
                      │
               Bind Mount (mp0)
                      │
          ┌────────────────────────┐
          │   LXC 102 - Fileserver │
          │      /mnt/files        │
          └──────────┬─────────────┘
                     │
                 Samba (SMB)
                     │
      ┌──────────────┼──────────────┐
      │              │              │
    macOS         Windows        iPhone
```

---

## Container Configuration

| Setting | Value |
|----------|-------|
| Platform | Proxmox VE |
| Container Type | Unprivileged LXC |
| Operating System | Debian |
| Storage | Bind mount from host |
| Export Path | `/mnt/files` |

---

## Samba Share Configuration

```ini
[files]
    comment = Homelab Files
    path = /mnt/files

    browseable = yes
    writable = yes
    read only = no

    guest ok = no

    valid users = <user>
    write list = <user>

    force user = <user>
    force group = <user>

    create mask = 0664
    directory mask = 0775

    force create mode = 0664
    force directory mode = 0775
```

---

## Permission Strategy

The fileserver runs inside an unprivileged LXC container.

Instead of storing data inside the container root filesystem, the storage is mounted from the Proxmox host using a bind mount.

This provides several advantages:

- persistent data independent of the container
- simplified backups
- easier disaster recovery
- container recreation without data loss

Permissions are managed on the host and mapped correctly into the container.

---

## Apple Client Compatibility

### Problem

macOS clients were able to read and write successfully.

iPhone clients authenticated correctly but only had read access and were unable to:

- create folders
- upload files
- modify existing content

---

### Root Cause

The Samba configuration lacked Apple-specific Virtual File System (VFS) modules required for full compatibility with iOS.

---

### Solution

The following options were added to the global Samba configuration:

```ini
[global]

server min protocol = SMB2
server max protocol = SMB3

vfs objects = catia fruit streams_xattr

fruit:aapl = yes
fruit:metadata = stream
fruit:resource = stream
fruit:nfs_aces = no

ea support = yes
```

After validating the configuration

```bash
testparm
```

and restarting Samba

```bash
systemctl restart smbd
```

the iPhone gained full read/write functionality.

---

## Verification

### Check active SMB sessions

```bash
smbstatus
```

### Verify filesystem permissions

```bash
ls -lah /mnt/files
```

### Validate Samba configuration

```bash
testparm
```

---

## Lessons Learned

- Apple devices require additional Samba compatibility modules (`fruit`, `catia`, `streams_xattr`) for full SMB functionality.
- Successful authentication does not necessarily imply write permissions.
- Testing with multiple client platforms helps identify platform-specific issues early.
- Hosting persistent storage outside the container simplifies maintenance and recovery.
- Unprivileged LXC containers require careful consideration of ownership and UID/GID mappings.

---

## Current Status

The SMB fileserver has been successfully validated with:

- ✅ macOS
- ✅ Windows
- ✅ iPhone / iPad

Read and write operations function correctly across all supported client platforms.
