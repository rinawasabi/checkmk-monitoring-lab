# Checkmk Monitoring Lab

Personal monitoring lab built on an Ubuntu Server virtual machine.

This repository documents a local infrastructure monitoring lab using Checkmk, Linux system services, NGINX, Apache reverse proxy, HTTPS, an internal lab CA and Checkmk Agent TLS.

## Goals

* Learn Checkmk administration
* Monitor a Linux server with the Checkmk agent
* Monitor Linux services and system resource usage
* Create a custom Checkmk local check for NGINX
* Configure HTTPS access using Apache reverse proxy
* Practice internal CA and certificate management
* Monitor HTTPS endpoint and certificate validity
* Configure Checkmk Agent TLS registration
* Practice Linux troubleshooting and service management

## Environment

* Host OS: Windows 11
* Virtualization: Oracle VirtualBox
* Guest OS: Ubuntu Server 26.04
* Monitoring: Checkmk 2.5
* Web server: NGINX
* Reverse proxy: Apache
* Network mode: Bridged Adapter


## Architecture

```text
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

* Ubuntu Server VM setup
* Bridged Adapter network configuration
* SSH access from Windows host
* Checkmk server installation
* Linux host monitoring with Checkmk agent
* Service discovery and monitoring activation
* System resource monitoring
* Memory threshold adjustment
* NGINX monitoring with a custom Checkmk local check
* NGINX failure detection test
* Apache HTTPS reverse proxy configuration
* Internal CA and server certificate setup
* HTTPS access to the Checkmk web interface
* Ubuntu system trust store configuration
* HTTPS endpoint monitoring with Checkmk active checks
* SSL certificate validity monitoring
* Checkmk Agent TLS registration


## Example Screenshots

### Dashboard

![Dashboard](screenshots/01-main-dashboard.png)

### Services

![Services](screenshots/03-services.png)

### NGINX Monitoring

![NGINX Monitoring](screenshots/11-nginx-ok.png)


## Troubleshooting Examples

* NAT networking made Checkmk web access from the Windows host inconvenient, so the VM was changed to Bridged Adapter mode.
* The default Apache service failed because NGINX was already using port `80`. NGINX was moved to port `8081`.
* HTTPS access stopped after reboot because the system Apache service was not enabled. This was fixed with `sudo systemctl enable --now apache2`.
* The HTTPS active check initially failed with `UnknownIssuer` because the Ubuntu server did not trust the internal lab Root CA. The Root CA was added to the Ubuntu trust store.
* Agent TLS registration initially failed with `401 Unauthorized` because the automation secret was incorrect. The secret was checked and registration was repeated successfully.

## Future Improvements

* Add another monitored Linux host
* Add more custom local checks
* Add notification rules
* Add backup monitoring
* Add HTTP to HTTPS redirection
* Add more detailed troubleshooting documentation
