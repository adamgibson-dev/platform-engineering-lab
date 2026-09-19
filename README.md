# Platform Engineering Lab

A hands-on DevOps and platform engineering lab built with Microsoft Hyper-V.

## Current Architecture

The lab currently consists of two Linux servers:

- Ubuntu Server 24.04 LTS
- CentOS Stream 10

Both virtual machines use two network interfaces.

### Internet Network

Each VM connects to the Hyper-V Default Switch for internet access using DHCP.

### Private DevOps Network

A dedicated Hyper-V private switch named `DevOps-Lab` provides isolated communication between lab systems.

| System | Private IP |
|---|---|
| Ubuntu | 10.10.10.10/24 |
| CentOS | 10.10.10.20/24 |

## Current Capabilities

- Ubuntu Server running on Hyper-V
- CentOS Stream running on Hyper-V
- Private communication between Linux servers
- Static addressing on the DevOps network
- Internet connectivity through separate virtual NICs
- SSH remote administration
- Ubuntu and CentOS can communicate over the private network

## Planned Technologies

- Git and GitHub
- Bash
- Python
- Ansible
- Docker
- Podman
- Terraform
- CI/CD
- Kubernetes
- Prometheus
- Grafana
- Linux security and hardening

## Purpose

The purpose of this project is to build practical DevOps and platform engineering skills through hands-on infrastructure, automation, networking, troubleshooting, and documentation.
