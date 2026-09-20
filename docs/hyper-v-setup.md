# Hyper-V Lab Setup

## Overview

This DevOps lab runs on Microsoft Hyper-V on a Windows 11 Pro host.

The environment currently contains two Linux virtual machines:

- Ubuntu Server 24.04 LTS
- CentOS Stream 10

The goal of the Hyper-V configuration is to provide each VM with:

1. Internet connectivity for updates and package installation
2. A separate private network for DevOps lab communication

---

## Hyper-V Host

Host operating system:

- Windows 11 Pro

Virtualization platform:

- Microsoft Hyper-V

The Hyper-V host provides the compute, memory, storage, and virtual networking used by the Linux servers.

---

## Virtual Switch Design

The lab uses two Hyper-V virtual switches.

### Default Switch

The built-in Hyper-V Default Switch is used for external connectivity.

It provides:

- D
HCP addressing
- NAT
- Internet access
- Connectivity from the Windows host

Each Linux VM has one virtual network adapter connected to the Default Switch.

The IP addresses on this network may change because they are assigned dynamically.

For this reason, the Default Switch is used for internet connectivity rather than permanent lab addressing.

---

### DevOps-Lab Private Switch

A second Hyper-V virtual switch was created specifically for the lab.

Switch name:

`DevOps-Lab`

Switch type:

`Private`

The switch was created from an elevated Windows PowerShell session:

```powershell
New-VMSwitch -SwitchName "DevOps-Lab" -SwitchType Private
```

A private Hyper-V switch allows the virtual machines connected to it to communicate with each other while keeping that traffic isolated from the physical network.

The DevOps network uses:

`10.10.10.0/24`

Current static addresses:

| System | Address |
|---|---|
| Ubuntu Server | `10.10.10.10/24` |
| CentOS Stream | `10.10.10.20/24` |

---

## Dual-NIC Design

Each virtual machine contains two virtual network adapters.

### NIC 1

Connected to:

`Default Switch`

Purpose:

- Internet access
- DHCP
- Operating system updates
- Package downloads
- SSH access from the Windows host

### NIC 2

Connected to:

`DevOps-Lab`

Purpose:

- Stable private IP addressing
- Linux-to-Linux communication
- SSH between lab servers
- Future Ansible automation
- Future container communication
- Monitoring and infrastructure services

The private NIC does not use a default gateway.

Internet-bound traffic continues to use the NIC connected to the Default Switch.

---

## Ubuntu Virtual Machine

VM name:

`ubuntu-devops`

Configuration:

- Operating System: Ubuntu Server 24.04 LTS
- Hyper-V Generation: 2
- vCPU: 4
- RAM: 8 GB
- Dynamic Memory: Disabled
- Virtual Disk: 60 GB
- NIC 1: Default Switch
- NIC 2: DevOps-Lab
- Secure Boot: Enabled
- Secure Boot Template: Microsoft UEFI Certificate Authority

Private IP:

`10.10.10.10/24`

---

## CentOS Virtual Machine

VM name:

`centos-devops`

Configuration:

- Operating System: CentOS Stream 10
- Hyper-V Generation: 2
- vCPU: 4
- RAM: 8 GB
- Dynamic Memory: Disabled
- Virtual Disk: 60 GB
- NIC 1: Default Switch
- NIC 2: DevOps-Lab
- Secure Boot: Enabled
- Secure Boot Template: Microsoft UEFI Certificate Authority

Private IP:

`10.10.10.20/24`

---

## Memory Configuration

Both VMs currently use fixed memory instead of Hyper-V Dynamic Memory.

Current allocation:

`8 GB per VM`

Dynamic Memory is disabled to provide predictable resource availability and simplify troubleshooting during the early stages of the lab.

This configuration can be revisited later as the environment grows.

---

## Current Hyper-V Topology

```text
                         Internet
                            |
                            |
                     Windows 11 Pro
                       Hyper-V Host
                            |
               +------------+------------+
               |                         |
               |                         |
        Default Switch               DevOps-Lab
          DHCP / NAT               Private Switch
               |                    10.10.10.0/24
               |                         |
          +----+----+               +----+----+
          |         |               |         |
       Ubuntu     CentOS          Ubuntu     CentOS
        DHCP       DHCP          .10         .20
```

Each Linux server therefore has one interface for external connectivity and another for private lab traffic.

---

## Design Benefits

This architecture provides several benefits.

### Predictable Lab Addresses

The private interfaces use static addresses, allowing automation tools to reliably locate each server.

### Network Separation

Internet traffic and internal DevOps traffic use separate virtual interfaces.

### Automation Ready

Future Ansible inventory files can reference permanent addresses such as:

```ini
[linux_servers]
ubuntu-devops ansible_host=10.10.10.10
centos-devops ansible_host=10.10.10.20
```

### Expandability

Additional servers can later be added to the DevOps-Lab network for:

- Kubernetes
- CI/CD systems
- Monitoring
- Logging
- Container hosts
- Infrastructure automation
- Security testing

---

## Current Status

The Hyper-V foundation is operational.

Completed:

- Hyper-V virtual machines created
- Ubuntu Server installed
- CentOS Stream installed
- Internet connectivity established
- Private DevOps-Lab switch created
- Static private addresses assigned
- Ubuntu and CentOS successfully communicate across the private network
- SSH connectivity established
EOF


