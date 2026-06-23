# 07 - Next Steps

## Overview

This document outlines possible next steps for expanding the current homelab environment.

The goal is to extend the setup gradually while keeping the overall architecture lightweight, maintainable and useful for practical systems administration, networking and self-hosting practice.

---

## Design Principles

Future additions should follow the same principles as the current setup:

* Keep services isolated using LXC containers or VMs.
* Separate application data from system files.
* Prefer lightweight services where possible.
* Document installation, configuration and maintenance steps.
* Test backup and restore procedures before relying on services.
* Avoid adding unnecessary complexity too early.
* Expand the network architecture step by step.

---

## Planned Expansion Areas

```text
                           Internet
                                │
                         Home Router
                                │
                    ┌────────────────────┐
                    │   Proxmox Host      │
                    └─────────┬───────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
   Existing Services      Network Lab          Future Services
        │                     │                     │
   Pi-hole LXC           OpenWrt VM        Monitoring / Backup
   Fileserver LXC        VLAN Testing      Reverse Proxy / VPN
   Joplin LXC            Firewall Rules    Nextcloud / Paperless
        │                     │                     │
        └─────────────────────┴─────────────────────┘
                              │
                        Host Storage
                           /mnt/data
```

---

## Network and Routing

### OpenWrt

OpenWrt is one of the planned next steps for building a small routing and firewall lab.

Possible goals:

* Install OpenWrt as a VM inside Proxmox.
* Create a separate test network.
* Configure LAN and WAN interfaces.
* Practice DHCP and DNS configuration.
* Test basic firewall rules.
* Understand NAT and port forwarding.
* Explore VLAN-based network separation.

Purpose:

OpenWrt would make it possible to practice routing, firewalling and network segmentation without changing the main home router configuration.

---

### VLAN Test Network

A VLAN-based test network could be added later to separate different parts of the homelab.

Possible segments:

* Management network
* Server network
* Client network
* Guest or test network
* IoT network

Purpose:

This would provide practical experience with network segmentation, isolation and basic security design.

---

## Monitoring and Logging

### Uptime Kuma

Uptime Kuma could be used for simple service availability monitoring.

Possible targets:

* Pi-hole
* Samba fileserver
* Joplin Server
* Proxmox web interface
* Future Docker-based services

Purpose:

This would provide a simple dashboard showing whether important services are reachable.

---

### Grafana and Prometheus

A more advanced monitoring setup could be added later.

Possible metrics:

* CPU usage
* Memory usage
* Disk usage
* Network traffic
* Container and VM status
* Service availability

Purpose:

Grafana and Prometheus would provide a better understanding of system load, resource usage and long-term behavior.

---

## Backup and Recovery

### Proxmox Backup Server

Proxmox Backup Server is a possible addition for structured VM and container backups.

Possible goals:

* Install Proxmox Backup Server as a VM or on separate hardware.
* Create backup jobs for selected containers.
* Define retention rules.
* Test restore procedures.
* Document backup and recovery steps.

Purpose:

The focus is not only on creating backups, but also on verifying that services can actually be restored.

---

### Offsite Backup

An additional offsite backup strategy could be added for important data.

Possible options:

* External USB drive
* Encrypted cloud backup
* rsync
* rclone
* Manual backup rotation

Purpose:

This would reduce the risk of data loss caused by hardware failure, misconfiguration or accidental deletion.

---

## Self-Hosted Applications

### Nextcloud

Nextcloud could be used as a self-hosted file, calendar and contact platform.

Possible goals:

* Install Nextcloud using Docker or LXC.
* Separate application and data storage.
* Configure users and access rights.
* Test mobile and desktop synchronization.
* Define backup and restore procedures.

---

### Paperless-ngx

Paperless-ngx could be used for document management and OCR-based archiving.

Possible goals:

* Deploy Paperless-ngx using Docker.
* Configure document import folders.
* Test OCR processing.
* Define tags and document types.
* Include documents and database in backup planning.

---

### Vaultwarden

Vaultwarden could be used as a lightweight self-hosted password manager.

Possible goals:

* Deploy Vaultwarden using Docker.
* Configure user access.
* Secure the service before external access.
* Define backup procedures for the database.
* Test access from multiple devices.

---

### Home Assistant

Home Assistant could be used for basic smart home and automation experiments.

Possible goals:

* Install Home Assistant as a VM or container.
* Integrate selected devices.
* Create simple automations.
* Build a small dashboard.
* Evaluate network and security implications of IoT devices.

---

## Web Access and Security

### Reverse Proxy

A reverse proxy could be added to manage access to internal web services.

Possible options:

* Nginx Proxy Manager
* Caddy
* Traefik

Possible goals:

* Use subdomains for internal services.
* Configure HTTPS certificates.
* Route requests to different containers.
* Restrict access where necessary.
* Document exposed services clearly.

---

### VPN Access

A VPN setup could provide secure remote access to the homelab.

Possible options:

* WireGuard
* Tailscale
* OpenVPN

Possible goals:

* Access internal services remotely.
* Avoid exposing services directly to the internet.
* Practice key management.
* Document client setup and access rules.

---

## Documentation Improvements

### Network Diagram

A dedicated network diagram should be created as the environment grows.

It should include:

* Proxmox host
* Containers and VMs
* IP addresses
* Storage paths
* Exposed ports
* Backup targets
* Network segments

---

### Service Documentation

Each service should have a short documentation file.

Suggested structure:

```text
Service name
Purpose
Deployment type
Container or VM ID
IP address
Ports
Storage paths
Backup strategy
Restore notes
Known issues
```

Purpose:

This makes the environment easier to maintain and helps with troubleshooting after changes.

---

## Suggested Priority

Short term:

1. Create a network diagram.
2. Finalize backup and restore documentation.
3. Add Uptime Kuma for basic monitoring.
4. Plan an OpenWrt test VM.

Medium term:

1. Test OpenWrt routing in an isolated lab network.
2. Add VPN access with WireGuard or Tailscale.
3. Evaluate Proxmox Backup Server.
4. Add a reverse proxy for internal services.

Long term:

1. Build a VLAN-based network structure.
2. Add Nextcloud or Paperless-ngx.
3. Expand monitoring with Grafana and Prometheus.
4. Improve security and backup procedures.
5. Document all services in a consistent format.

---

## Current Status

The homelab currently focuses on basic virtualization, self-hosted services, storage access and documentation.

The next major learning areas are:

* Network segmentation
* Routing and firewalling
* Monitoring
* Backup and recovery
* Secure remote access
* Additional self-hosted applications
