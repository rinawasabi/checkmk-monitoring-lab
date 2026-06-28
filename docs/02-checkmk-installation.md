# Checkmk Installation

## Objective

The objective was to install Checkmk on an Ubuntu Server VM and deploy a working web-based monitoring environment.

## Environment

* Ubuntu Server 26.04 LTS
* Oracle VirtualBox
* Checkmk 2.5
* Bridged Adapter networking

## Installation

The Checkmk installation package was downloaded from the official website and installed on the Ubuntu Server virtual machine.

A new Checkmk site named **monitoring** was then created using commands such as below.

```bash
sudo omd create monitoring
sudo omd start monitoring
```

## Site Management

The status of the Checkmk site was verified using:

```bash
sudo omd status monitoring
```

All services, including Apache, CMC, Redis and RRDCache, were confirmed to be running successfully.

## Network Configuration

Initially, the VM used a NAT network adapter. Although Checkmk was running correctly, the web interface could not be accessed from the host computer.

To simplify network communication, the VM was reconfigured to use a **Bridged Adapter**. This allowed the virtual machine to receive its own IP address on the local network.

The Apache listener was also configured to accept connections from the network instead of only localhost.

## Verification

The web interface was tested using:

```bash
curl -I http://127.0.0.1:5000/monitoring/check_mk/
```

The response confirmed that Apache redirected the request to the Checkmk login page (HTTP 302), indicating that the installation was successful.

Finally, the Checkmk login page was successfully accessed from a web browser using the virtual machine's IP address.

## Result

At the end of this phase, the followings were completed.

* Installed Checkmk on Ubuntu Server
* Created and started a Checkmk site
* Verified all monitoring services were running
* Configured network access using Bridged Adapter
* Successfully accessed the Checkmk web interface from a browser
