# Network Design

## Overview

The lab uses a dual-NIC design for each virtual machine.

Each VM has one interface connected to the Hyper-V Default Switch for internet access and one interface connected to the private `DevOps-Lab` switch for lab traffic.

## Ubuntu Server

Internet interface:

- Hyper-V Default Switch
- DHCP addressing

Private interface:

- Network: `DevOps-Lab`
- Address: `10.10.10.10/24`
- No default gateway
- No DNS configuration

## CentOS Stream

Internet interface:

- Hyper-V Default Switch
- DHCP addressing

Private interface:

- Network: `DevOps-Lab`
- Address: `10.10.10.20/24`
- No default gateway
- No DNS configuration

## Routing

Internet-bound traffic uses the DHCP interface.

Traffic destined for the `10.10.10.0/24` network uses the private DevOps interface.

This keeps lab communication separate from external network traffic.

## Connectivity Testing

Ubuntu can reach CentOS:

```bash
ping -c 4 10.10.10.20
