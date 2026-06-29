# Checkmk Monitoring Lab

Personal monitoring lab built on Ubuntu Server VM.

## Goals

- Learn Checkmk administration
- Monitor Linux services
- Monitor nginx
- Explore monitoring plugins
- Practice Linux system administration

## Environment

- Ubuntu Server (26.04)
- Checkmk Raw
- nginx
- VirtualBox

This repository is for learning and demonstrating Linux monitoring skills.

## Architecture

```textArchitecture
Host Computer
┌──────────────────────────────────────────────┐
│ Windows 11                                   │
│                                              │
│  Oracle VirtualBox                           │
│  ┌────────────────────────────────────────┐  │
│  │ Ubuntu Server 26.04                    │  │
│  │                                        │  │
│  │  ├── System Apache                     │  │
│  │  │     ├── HTTP  (:80)                 │  │
│  │  │     └── HTTPS (:443)                │  │
│  │  │          └── Reverse proxy          │  │
│  │  │              to Checkmk (:5000)     │  │
│  │  │                                     │  │
│  │  ├── Checkmk site Apache (:5000)       │  │
│  │  │                                     │  │
│  │  ├── Checkmk Agent                     │  │
│  │  │                                     │  │
│  │  └── NGINX (:8081)                     │  │
│  └────────────────────────────────────────┘  │
└──────────────────────────────────────────────┘

Browser
      │
      │ HTTPS
      ▼
https://<vm-ip>/monitoring/check_mk/
```

## Features
- Ubuntu Server VM setup
- Bridged Adapter network configuration
- SSH access from Windows host
- Checkmk server installation
- Linux host monitoring with Checkmk agent
- Service discovery and monitoring activation
- NGINX monitoring with a custom Checkmk local check
- Apache HTTPS reverse proxy configuration
- HTTPS access to the Checkmk web interface

## Example Screenshots

### Dashboard

![Dashboard](screenshots/01-main-dashboard.png)

### Services

![Services](screenshots/03-services.png)
