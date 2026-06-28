# Custom NGINX Monitoring with Checkmk Local Check

The purpose of this task was to create a custom Checkmk local check to monitor the status of the NGINX service.

## Implementation

A custom Bash script was created in the Checkmk local checks directory.

Location:

```text
/usr/lib/check_mk_agent/local/check_nginx_status
```

The script checks whether the NGINX service is running using `systemctl`. It returns an OK state when the service is active and a CRITICAL state when it is stopped.

## Integration with Checkmk

After creating the local check:

* The script was made executable.
* The Checkmk agent was verified.
* Service discovery was executed.
* The new service **NGINX_Status** was detected.
* The service was accepted and monitoring was activated.

## Validation

The monitoring was tested by intentionally stopping and restarting the NGINX service.

Commands used:

```bash
sudo systemctl stop nginx
sudo systemctl start nginx
```

Results:

* NGINX running → **OK**
* NGINX stopped → **CRITICAL**
* NGINX restarted → **OK**

This confirmed that the custom monitoring check correctly detected changes in the service state.

## Skills Practiced

* Linux service management
* Bash scripting
* Checkmk Local Checks
* Service discovery
* Monitoring validation
* Troubleshooting
