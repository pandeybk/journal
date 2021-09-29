## Sep 28 2020

- [Openstack installation](https://access.redhat.com/documentation/en-us/red_hat_openstack_platform/16.1/html/standalone_deployment_guide/all-in-one-openstack-installation)
- [Ashish Aggarwal's blog](https://github.com/rh-telco-tigers/OSP16.2-AIO)
- [Brandon Jozsa's gist](https://gist.github.com/v1k0d3n/f90fb378730d1c7c57b0a642d40e4262)
- [Cleanup Openstack](https://www.redhat.com/sysadmin/tripleo-standalone-system)
```
cat <<EOF > $HOME/standalone-cleanup.sh
#!/bin/bash
echo "Tearing down TripleO environment"
if type pcs &> /dev/null; then
    sudo pcs cluster destroy
fi
if type podman &> /dev/null; then
    echo "Removing podman containers and images (takes times...)"
    sudo podman rm -af
    sudo podman rmi -af
fi
sudo rm -rf \
    /var/lib/tripleo-config \
    /var/lib/config-data /var/lib/container-config-scripts \
    /var/lib/container-puppet \
    /var/lib/heat-config \
    /var/lib/image-serve \
    /var/lib/containers \
    /etc/systemd/system/tripleo* \
    /var/lib/mysql/*
sudo systemctl daemon-reload
EOF
```
- [OpenVS Switch Cheat Sheet](https://randomsecurity.dev/posts/openvswitch-cheat-sheet/)

## Sep 29 2020