## kvm cheatsheet
https://docs.openstack.org/nova/rocky/admin/configuration/hypervisor-kvm.html

- https://openshift.tips/

## Assisted Installer
- https://cloud.redhat.com/blog/using-the-openshift-assisted-installer-service-to-deploy-an-openshift-cluster-on-metal-and-vsphere
- Watch demo (TODO)
  https://www.youtube.com/watch?v=BNC-5ISbxpU&t=1157s

## Openshift on openstack
- https://docs.openshift.com/container-platform/4.8/installing/installing_openstack/installing-openstack-user.html

## Openshift troubleshooting
https://docs.openshift.com/container-platform/4.7/installing/installing_bare_metal_ipi/ipi-install-troubleshooting.html

- Unable to install openshift on openstack, keep seeing following error log on cloud.redhat.com console.
```  
Warning alert:Insufficient
valid-platform: Platform OpenStack Compute is allowed only for Single Node OpenShift or user-managed networking.
```

Event logs from individual machine. Hostname and ntp issue fixed automatically, but seeing Machine Network CIDR is undefined. 
```
10/1/2021, 12:32:17 PM	Host master2: validation 'ntp-synced' is now fixed
10/1/2021, 12:31:43 PM	Host master2: validation 'hostname-valid' is now fixed
10/1/2021, 12:31:43 PM	Host master2: validation 'hostname-unique' is now fixed
10/1/2021, 12:31:21 PM	
warning
 Host localhost: updated status from "discovering" to "insufficient" (Host does not meet the minimum hardware requirements: Host couldn't synchronize with any NTP server ; Hostname localhost is forbidden ; Hostname localhost is not unique in cluster ; Machine Network CIDR is undefined; the Machine Network CIDR can be defined by setting either the API or Ingress virtual IPs ; Platform OpenStack Compute is allowed only for Single Node OpenShift or user-managed networking)
10/1/2021, 12:31:13 PM	Host 2c07679b-b728-4401-a937-df23fc7b2eec: registered to cluster
```