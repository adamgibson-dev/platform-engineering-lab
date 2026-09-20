# CentOS Stream Setup

## Overview

CentOS Stream 10 is used as the Red Hat-family Linux system in the DevOps lab.

It provides experience with:

- NetworkManager
- DNF package management
- SELinux
- Red Hat-style system administration
- Future Ansible-managed-node configuration

## System Configuration

- Operating System: CentOS Stream 10
- Hostname: `centos-devops`
- Hyper-V Generation: 2
- vCPU: 4
- RAM: 8 GB fixed
- Dynamic Memory: Disabled
- Virtual Disk: 60 GB
- Secure Boot: Enabled
- Secure Boot Template: Microsoft UEFI Certificate Authority

## Network Design

The CentOS VM uses two virtual network adapters.

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

`10.10.10.20/24`

This interface is used for private communication with other systems in the lab.

It does not provide the default route.

## NetworkManager Configuration

CentOS uses NetworkManager for persistent network configuration.

The private interface was configured with:

    sudo nmcli connection modify eth1 \
    ipv4.method manual \
    ipv4.addresses 10.10.10.20/24 \
    ipv4.never-default yes \
    ipv6.method disabled \
    connection.autoconnect yes

This configuration:

- Disables DHCP on the private interface
- Assigns the static address `10.10.10.20/24`
- Prevents eth1 from becoming the default route
- Disables IPv6 on the private lab interface
- Enables automatic connection at boot

## Applying the Configuration

The connection was restarted with:

    sudo nmcli connection down eth1
    sudo nmcli connection up eth1

This forces NetworkManager to reload the updated connection profile.

## Verification

Network interfaces can be checked with:

    ip -br addr

The expected private interface is:

    eth1    UP    10.10.10.20/24

The routing table can be checked with:

    ip route

NetworkManager device status can be checked with:

    nmcli device status

## Hostname Configuration

The hostname was changed with:

    sudo hostnamectl set-hostname centos-devops

The hostname can be verified with:

    hostname

Expected result:

    centos-devops

## Connectivity Testing

CentOS can test connectivity to Ubuntu with:

    ping -c 4 10.10.10.10

Ubuntu can test connectivity to CentOS with:

    ping -c 4 10.10.10.20

Successful replies confirm communication across the private DevOps-Lab network.

## SSH

OpenSSH is used for remote administration.

CentOS can be accessed remotely from:

- MobaXterm on the Windows host
- Ubuntu over the DevOps-Lab network
- Future Ansible automation

## Current Role

CentOS currently provides:

- Red Hat-family Linux administration practice
- NetworkManager configuration practice
- Private network connectivity
- SSH access
- Future Ansible managed-node functionality
- Future Podman and container practice
