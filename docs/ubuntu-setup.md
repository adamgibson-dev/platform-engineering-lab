# Ubuntu Server Setup

## Overview

Ubuntu Server is used as one of the primary Linux systems in the DevOps lab.

It will also serve as the main administration and automation node as the lab expands.

## System Configuration

- Operating System: Ubuntu Server 24.04 LTS
- Hostname: `ubuntu-devops`
- Hyper-V Generation: 2
- vCPU: 4
- RAM: 8 GB fixed
- Dynamic Memory: Disabled
- Virtual Disk: 60 GB
- Secure Boot: Enabled
- Secure Boot Template: Microsoft UEFI Certificate Authority

## Network Design

The Ubuntu VM uses two virtual network adapters.

### eth0 - Internet Interface

`eth0` connects to the Hyper-V Default Switch.

Purpose:

- DHCP addressing
- Internet connectivity
- Package downloads
- Operating system updates
- SSH access from the Windows host

The IP address on this interface is dynamically assigned and may change.

### eth1 - DevOps-Lab Interface

`eth1` connects to the private Hyper-V switch named:

`DevOps-Lab`

Static address:

`10.10.10.10/24`

This interface is used for communication with other systems in the DevOps lab.

It does not have a default gateway because internet traffic uses `eth0`.

## Netplan Configuration

Ubuntu uses Netplan to manage persistent network configuration.

A separate configuration file was created for the private interface:

`/etc/netplan/99-devops-lab.yaml`

Configuration:

    network:
      version: 2
      ethernets:
        eth1:
          dhcp4: false
          dhcp6: false
          addresses:
            - 10.10.10.10/24
          optional: true

The private interface uses a manually assigned IPv4 address instead of DHCP.

## Netplan File Permissions

Netplan warned that the configuration file permissions were too open.

The permissions were corrected with:

    sudo chmod 600 /etc/netplan/99-devops-lab.yaml

Permission mode `600` means:

- Root can read the file
- Root can write to the file
- Other users have no access

## Applying Network Configuration

The configuration was checked with:

    sudo netplan generate

This validates the Netplan configuration and generates the underlying network configuration.

The changes were then applied with:

    sudo netplan apply

## Verification

The network interfaces can be checked with:

    ip -br addr

The expected private interface is:

    eth1    UP    10.10.10.10/24

The routing table can be checked with:

    ip route

Traffic for the private lab network should use `eth1`.

Internet traffic should continue to use the default route through `eth0`.

## SSH

OpenSSH Server was installed during the Ubuntu installation.

This allows Ubuntu to be administered remotely using MobaXterm.

SSH will also be used later by automation tools such as Ansible.

## Resource Verification

Memory usage can be checked with:

    free -h

The VM currently uses 8 GB of fixed Hyper-V memory.

## Current Role

Ubuntu currently provides:

- Linux administration practice
- Private network connectivity
- SSH access
- Git and GitHub access
- Future Ansible control-node functionality
- Future DevOps tooling
