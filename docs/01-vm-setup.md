# Ubuntu Server Virtual Machine Setup

This environment will later be used to deploy Checkmk and practice Linux server monitoring.

## Environment

* Host OS: Windows 11
* Virtualization: Oracle VirtualBox
* Guest OS: Ubuntu Server 26.04 LTS

## Virtual Machine Configuration

The Ubuntu Server virtual machine was created using Oracle VirtualBox.
Initially, the virtual machine used a NAT network adapter to provide internet access during the operating system installation.
After installing Checkmk, the network adapter was changed to **Bridged Adapter**. 
This allowed the virtual machine to get its own IP address on the local network, making the Checkmk web interface directly accessible from the host computer.

## SSH Access

SSH was enabled to allow remote administration of the Ubuntu server from the Windows host.
After switching to Bridged Adapter networking, the server became accessible using its local IP address.

Example:

```bash
ssh rina@192.168.0.108
```

## Network Verification

The network configuration was verified using:

```bash
hostname -I
```

This confirmed that the virtual machine received an IP address from the local network.

## Result

At the end of this phase, the following tasks had been completed:

* Ubuntu Server virtual machine created
* Network connectivity configured
* Bridged Adapter networking enabled
* SSH remote access verified
* Linux server ready for monitoring software installation
