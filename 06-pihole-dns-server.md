# 06 - Pi-hole DNS Server

## Overview

This document describes the deployment of Pi-hole as the network-wide DNS server within the homelab environment.

Pi-hole provides centralized DNS filtering and advertisement blocking for all connected devices while also acting as a lightweight internal infrastructure service.

Running Pi-hole inside a dedicated LXC container on Proxmox keeps the service isolated, easy to maintain and resource-efficient.

---

## Objectives

The deployment was designed with the following goals:

* Provide network-wide advertisement and tracker blocking.
* Centralize DNS resolution for all local devices.
* Reduce unnecessary external network traffic.
* Improve privacy by limiting third-party DNS requests.
* Create a lightweight infrastructure service requiring minimal system resources.

---

## Architecture

```text
                    Internet
                         │
                    Router / ISP
                         │
                  ┌──────────────┐
                  │   Fritz!Box  │
                  └──────┬───────┘
                         │
                  DNS Requests
                         │
                  ┌──────────────┐
                  │   Pi-hole    │
                  │   LXC 101    │
                  └──────┬───────┘
                         │
                  Upstream DNS
                         │
                   Public Resolver
```

---

## Container Configuration

| Setting          | Value            |
| ---------------- | ---------------- |
| Platform         | Proxmox VE       |
| Container Type   | Unprivileged LXC |
| Operating System | Debian           |
| Purpose          | DNS Filtering    |
| Startup          | Automatic        |

Pi-hole operates as a dedicated infrastructure component and remains isolated from application workloads.

---

## Network Integration

Client devices use Pi-hole as their primary DNS resolver.

Typical clients include:

* macOS
* Windows
* iPhone / iPad
* Self-hosted services
* LXC containers

This ensures consistent DNS filtering across the local network.

---

## Deployment Considerations

Hosting Pi-hole inside an LXC container provides several operational advantages:

* low memory usage
* rapid deployment
* simplified maintenance
* easy snapshot creation
* isolated service management

Because DNS is a critical infrastructure component, the container is configured to start automatically together with the Proxmox host.

---

## Operational Benefits

Centralized DNS filtering provides:

* reduced advertisements
* blocked tracking domains
* improved browsing performance
* simplified network administration
* consistent filtering across all supported devices

No client-side software installation is required beyond configuring DNS.

---

## Maintenance

Routine maintenance includes:

* updating blocklists
* applying Pi-hole updates
* monitoring query statistics
* verifying upstream DNS availability
* reviewing false positives when necessary

The lightweight nature of the service allows maintenance with minimal downtime.

---

## Lessons Learned

* Infrastructure services should be deployed before application services whenever possible.
* DNS failures affect the entire environment and should therefore remain simple and reliable.
* LXC containers provide an efficient platform for lightweight network services.
* Centralized DNS filtering significantly improves consistency across heterogeneous client devices.
* Isolating DNS from application workloads simplifies troubleshooting.

---

## Current Status

The Pi-hole deployment is fully operational.

Current capabilities include:

* ✅ network-wide DNS resolution
* ✅ advertisement blocking
* ✅ tracker blocking
* ✅ lightweight LXC deployment
* ✅ automatic startup with the Proxmox host

The service forms one of the foundational infrastructure components of the homelab environment.
