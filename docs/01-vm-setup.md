# Ubuntu Server Virtual Machine Setup

## Purpose

This document describes the initial setup of an Ubuntu Server virtual machine for a personal Checkmk monitoring lab.

## Environment

- Host OS: Windows 11
- Virtualization: Oracle VirtualBox
- Guest OS: Ubuntu Server 26.04 LTS

## Virtual Machine Setup

An Ubuntu Server virtual machine was created using Oracle VirtualBox. The VM was configured as a lightweight Linux server environment for monitoring practice.

During the initial installation, the VM used NAT networking to access the internet and complete package installation.

## Network Configuration

After the base installation, the network mode was changed from NAT to Bridged Adapter. This made the VM available as a separate device on the local network and allowed access from the Windows host using the VM's local IP address.

## SSH Access

SSH was enabled for remote administration from the Windows host.
```bash
ssh username@<vm-ip-address>
```

## Verification

The VM's IP address was checked with:
```
hostname -I
```

## Result

The Ubuntu Server VM was successfully created and prepared for Checkmk installation.
