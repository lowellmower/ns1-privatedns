# NS1 Enterprise DDI Nagios Monitoring Plugin

## Summary

Operators of NS1 Enterprise DDI should monitor their DDI deployment for health checks and functionality. Nagios can be used to facilitate this type of testing. This folder contains a script to check the health check output of an NS1 DDI container and an example Nagios config file that uses this check script.

## Health Check Script (check_ddi.py)

### Requirements

* Python 3.6 or higher
* Python requests library
* `supd` webserver must be reachable from the Nagios server

### Usage

The check script takes up to four arguments:

1. The host (name or IP address) running the container
2. The port that `supd` is listening on
3. The `supd` username (Optional - defaults to "ns1")
4. The `supd` password (Optional - defaults to "private")

```shell
$ python3 check_ddi.py 192.168.56.2 3300
CRITICAL - failed to connect to container
```

### Outputs

The `check_ddi.py` script has three possible outputs:

1. `CRITICAL - failed to connect to container` (exit code 2): The check script could not connect to the `supd` API. Potential causes include: `supd` is down/unhealthy, the `supd` port is not exposed on the host, the incorrect `supd` port or host was specified, the host is not reachable, and/or some other condition has impeded the check script from getting a response.
2. `CRITICAL - failed health check: <failed checks>` (exit code 2): The `supd` API responded with health check information and one or more health checks failed, the failing health checks are indicated in the message.
3. `WARNING - check could not run, please supply host and port` (exit code 1) indicates that the host or port were not suplied to the script. The health check script did not attempt to connect to the `supd` API.
4. `OK - all checks passed` (exit code 0): The the check script was called properly, the `supd` API responded properly, and all health checks are passing.

## Example Nagios Config

The `nagios-config-example.cfg` file contains sample Nagios host, service, and command definitions.  Save the contents of this file to your Nagios conf.d directory (/usr/local/nagios/etc/conf.d by default).  You will need to modify host_name, alias, address, and possibly other elements depending on your nagios configuration.

The example configuration is divided into two sections.  The first section defines a command and services around the DDI `supd` API.  The service definitions may need to be modified if you are using port mappings other than those exposed by default in `docker-compose.yaml`.  The second section provides examples of how to use the Nagios check_dns and check_dhcp plugins to perform synthetic tests against the NS1 DDI platform.
