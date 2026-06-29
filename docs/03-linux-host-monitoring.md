# Linux Host Monitoring with Checkmk Agent


This page describes how the Ubuntu Server VM was added as a monitored Linux host in Checkmk.

## What I did

- Added the Ubuntu VM as a host in Checkmk
- Verified network connectivity with ping
- Installed the Checkmk Linux agent on the Ubuntu VM
- Ran service discovery & accepted discovered services
- Confirmed that system services and resource usage were visible in Checkmk

## Monitored Services

After service discovery, Checkmk detected several Linux system metrics, such as

- CPU usage
- Memory usage
- Filesystem usage
- Network interfaces
- Systemd services
- Checkmk agent status

## Memory Threshold Adjustment

Checkmk detected that total virtual memory usage exceeded the default warning threshold. To reduce unnecessary warnings, I adjusted the memory monitoring rule.

- Warning: 90%
- Critical: 95%

## Troubleshooting

During monitoring, the `apache2` service was shown as failed. Investigation showed that nginx was already using port 80:

```bash
sudo ss -tlnp | grep -E ':80|:5000'
```

The result:
nginx was listening on port 80, while Checkmk Apache was listening on port 5000. This confirmed that the failed apache2 service was caused by a port conflict with nginx.


## Checkmk Agent TLS Registration

After the Checkmk Linux agent was installed, the `Check_MK Agent` service showed a warning because TLS was not activated on the monitored host. This warning was related to the communication between the Checkmk server and the Checkmk agent.

To activate Agent TLS, the Agent Controller was registered with the Checkmk site using `cmk-agent-ctl`.

The registration used the followings:

```text
Hostname: ubuntu-monitoring
Server: 192.168.0.108:8000
Site: monitoring
User: agent_registration
```

After registration, the agent connection was verified with `cmk-agent-ctl status`.

The `Check_MK Agent` service changed from WARN to OK, which means that TLS communication between the Checkmk server and the monitored Linux host was active.


## Result

The Ubuntu Server VM is now monitored by Checkmk using the Linux agent.
The monitoring setup can detect system resource usage, service states and configuration issues.
