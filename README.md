# SiaB Submit Host Condor Configuration Templates

This repository includes the condor configuration files needed for any Site-in-a-Box (SiaB) submit host to connect to the SiaB collector. 

## Pre-requisites
In order to deploy a SiaB submit host, the system administrator needs to meet the following pre-requisites first:

### Install HTCondor
If not already installed, you can install HTCondor e.g: via yum using [HTCondor](https://research.cs.wisc.edu/htcondor/yum) or [OSG](https://opensciencegrid.github.io/docs/common/yum/) repositories.

### Get a Host Certificate for the submit host
 - Please, use the following [OSG documentation link](https://opensciencegrid.github.io/docs/security/host-certs) in order to get the host certificate for the submit host, if none present.
 - Once a host certificate is available, the DN can be obtained via:
   ```
   $ openssl x509 -in /etc/grid-security/hostcert.pem -noout -subject
   subject= /DC=org/DC=opensciencegrid/O=Open Science Grid/OU=Services/CN=<hostname_domain>
   ```
 - The DN above will need to be whitelisted by the SiaB collector. Please, contact the USCMS Tier 3 Team (email: uscms-tier3-team@cern.ch) to get this step done.

### Whitelist condor shared port in firewall
In order to get the SiaB pilots connecting to the submit host, a special TCP shared port is used: 9615. The system administrator needs to whitelist this port for this to happen.
In case the host needs to change this port, this can be done using the attribute `SHARED_PORT_ARGS` in [config.d/02_gwms_schedds.config](condor/config.d/02_gwms_schedds.config#L64)
- The following lines can be added to `/etc/sysconfig/iptables` to whitelist this port using iptables:
```
# Allow condor queries on schedd through port 9615
-A INPUT -m state --state NEW -p tcp -m tcp --dport 9615 -j ACCEPT
```
## Configuring condor
Once the pre-requisites have been met, copying `config.d` and doing `service condor restart` on the submit host should make things work. 

## Testing condor setup
- To check if the collector is seeing the submit host, you can type the following from the submit host:
```
$ condor_status -pool $(condor_config_val COLLECTOR_HOST) -schedd
```
- For further testing, please use the condor tests in the [following repository](https://github.com/USCMSTier3/siab-tests).
