## Sep 29, 2021

- User/Role/Project
https://docs.openstack.org/keystone/pike/admin/cli-manage-projects-users-and-roles.html
https://docs.openstack.org/ocata/user-guide/cli-cheat-sheet.html

```
# Create demo project
openstack project create goglides-demo --description "project for demo purpose" --domain default 

# Create User
openstack user create --project goglides-demo --password secretspass123 goglides-demo-user1

# Create role
openstack role create goglides-demo-role1

# Add role to project
openstack role add --user goglides-demo-user1 --project goglides-demo goglides-demo-role1
```

- Create cirros server
[Get openstack OS images](https://docs.openstack.org/image-guide/obtain-images.html)
```
curl -LO http://download.cirros-cloud.net/0.5.2/cirros-0.5.2-x86_64-disk.img
openstack server create --image cirros-0.5.2-x86_64-disk --flavor m1.tiny goglide-cirros-demo
openstack flavor create --ram 512 --disk 1 --vcpus 1 m1.tiny
openstack server create --image cirros-threepart-kernel --flavor m1.tiny goglide-cirros-demo

# If you have multiple network you have to pass network name/id
openstack server create --image cirros-0.5.2-x86_64-disk --flavor m1.tiny goglide-cirros-demo2 --network=external-network
```

- [Undercloud/Overcloud ??](https://access.redhat.com/documentation/en-us/red_hat_openstack_platform/8/html/director_installation_and_usage/chap-introduction)

- Method 1: 
```
openstack --os-username demo \
--os-password openstack \
--os-project-name demo \
--os-user-domain-name Default \
--os-project-domain-name Default \
--os-auth-url http://192.168.7.249:35357/v3 \
--os-identity-api-version 3 \
[command] [options]
```

- [By Brandon Jozsa, Sushy Emulator Redfish for virtualization](https://cloudcult.dev/sushy-emulator-redfish-for-the-virtualization-nation/)

```
bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock-ro' net-dumpxml default
bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-define ovs-trunk.xml 
Network ovs-trunk defined from ovs-trunk.xml

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-auto-start ovs-trunk
error: unknown command: 'net-auto-start'
bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-autostart ovs-trunk 
Network ovs-trunk marked as autostarted

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-start ovs-trunk 
Network ovs-trunk started

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-list          
 Name        State    Autostart   Persistent
----------------------------------------------
 default     active   yes         yes
 ovs-trunk   active   yes         yes
```

# Create following files

```
cat <<EOF > ovs-trunk.xml
<network>
  <name>ovs-trunk</name>
  <forward mode='bridge'/>
  <bridge name='ovs-trunk'/>
  <virtualport type='openvswitch'/>
  <portgroup name='lab-trunk'>
    <vlan trunk='yes'>
      <tag id='2' />
      <tag id='3' />
      <tag id='4' />
      <tag id='22' />
      <tag id='60' />
      <tag id='70' />
      <tag id='88' />
      <tag id='90' />
      <tag id='101' />
      <tag id='102' />
      <tag id='103' />
      <tag id='204' />
      <tag id='219' />
      <tag id='254' />
      <tag id='482' />
      <tag id='555' />
      <tag id='999' />
      <tag id='1003' />
      <tag id='1004' />
    </vlan>
  </portgroup>
</network>
EOF

cat <<EOF > ovs-br60.xml
<network>
  <name>br60</name>
  <forward mode='bridge'/>
  <bridge name='ovs-trunk'/>
  <virtualport type='openvswitch'/>
  <portgroup name='br60'>
    <vlan>
      <tag id='60'/>
    </vlan>
  </portgroup>
</network>
EOF

cat <<EOF >ovs-br90.xml
<network>
  <name>br90</name>
  <forward mode='bridge'/>
  <bridge name='ovs-trunk'/>
  <virtualport type='openvswitch'/>
  <portgroup name='br90'>
    <vlan>
      <tag id='90'/>
    </vlan>
  </portgroup>
</network>
EOF
```

```
virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock-ro' net-define ovs-trunk.xml
virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' pool-list --all
```


```
bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-define ovs-br60.xml 
Network br60 defined from ovs-br60.xml

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-autostart br60
Network br60 marked as autostarted

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-start br60 
Network br60 started

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-define ovs-br90.xml
Network br90 defined from ovs-br90.xml

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-autostart br90
Network br90 marked as autostarted

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' net-start br90         
Network br90 started
```


# In openstack server run following

```
[root@goglides images]# mkdir -p /var/lib/libvirt/sushy-emulation/boot
[root@goglides images]# mkdir -p /var/lib/libvirt/sushy-emulation/default
[root@goglides images]# virsh pool-define-as boot --type dir --target /var/lib/libvirt/sushy-emulation/boot
Pool boot defined

[root@goglides images]# virsh pool-define-as default --type dir --target /var/lib/libvirt/sushy-emulation/default
Pool default defined
```

```
bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' pool-list --all
 Name   State   Autostart
---------------------------

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' pool-list --all
 Name   State      Autostart
------------------------------
 boot   inactive   no

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' pool-autostart boot
Pool boot marked as autostarted

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' pool-start boot 
Pool boot started

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' pool-start default
Pool default started

bpandey@Balkrishnas-MacBook-Pro openstack % virsh -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' pool-autostart default
Pool default marked as autostarted
```


## Sushy 

```
sudo mkdir -p /etc/sushy/
```

```
cat << "EOF" | sudo tee /etc/sushy/sushy-emulator.conf
SUSHY_EMULATOR_LISTEN_IP = u'0.0.0.0'
SUSHY_EMULATOR_LISTEN_PORT = 8000
SUSHY_EMULATOR_SSL_CERT = None
SUSHY_EMULATOR_SSL_KEY = None
SUSHY_EMULATOR_OS_CLOUD = None
SUSHY_EMULATOR_LIBVIRT_URI = u'qemu:///system'
SUSHY_EMULATOR_IGNORE_BOOT_DEVICE = True
SUSHY_EMULATOR_BOOT_LOADER_MAP = {
    u'UEFI': {
        u'x86_64': u'/usr/share/OVMF/OVMF_CODE.secboot.fd'
    },
    u'Legacy': {
        u'x86_64': None
    }
}
EOF
```

```
export SUSHY_TOOLS_IMAGE=${SUSHY_TOOLS_IMAGE:-"quay.io/metal3-io/sushy-tools"}
sudo podman create --net host --privileged --name sushy-emulator -v "/etc/sushy":/etc/sushy -v "/var/run/libvirt":/var/run/libvirt "${SUSHY_TOOLS_IMAGE}" sushy-emulator -i :: -p 8000 --config /etc/sushy/sushy-emulator.conf
```

## Create virtual machine

- in server
```
DOWNLOAD_URL='https://download.fedoraproject.org/pub/fedora/linux/releases/34/Server/x86_64/iso/Fedora-Server-dvd-x86_64-34-1.2.iso'
DOWNLOAD_NAME="Fedora-Server-dvd-x86_64-34-1.2.iso"
sudo sh -c 'curl -k -L '"${DOWNLOAD_URL}"' -o /var/lib/libvirt/sushy-emulation/boot/'"${DOWNLOAD_NAME}"'
```

- Create virtual machine
```
OCP_NODE="01"
VM_NAME="ocp$OCP_NODE"
VM_UUID="00000000-0000-0000-0060-0000000000$OCP_NODE"
VM_POOL="default"
VM_BOOT_PATH="/var/lib/libvirt/sushy-emulation/boot"
VM_BOOT_NAME="Fedora-Server-dvd-x86_64-34-1.2.iso"
VM_DISK_PATH="/var/lib/libvirt/sushy-emulation/default"
VM_DISK_SIZE="200"
VM_RAM="32768"
VM_CPU=8
VM_MAC="52:54:00:00:60:$OCP_NODE"
VM_BRIDGE="br60"

virt-install --connect 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock' \
  --import \
  --uuid=$VM_UUID \
  --name=$VM_NAME \
  --ram=$VM_RAM \
  --vcpus=$VM_CPU \
  --cpu host-passthrough \
  --os-type linux \
  --os-variant rhel8.4 \
  --noreboot \
  --events on_reboot=restart \
  --noautoconsole \
  --boot hd,cdrom \
  --import \
  --disk path=$VM_DISK_PATH/$VM_NAME.qcow2,size=$VM_DISK_SIZE,pool=$VM_POOL \
  --disk $VM_BOOT_PATH/$VM_BOOT_NAME,device=cdrom \
  --network network:$VM_BRIDGE,portgroup=$VM_BRIDGE,mac=$VM_MAC
```

```
virt-manager -c 'qemu+ssh://root@192.168.7.240/system?socket=/var/run/libvirt/libvirt-sock'
```

- Dell Memory channel (https://qrl.dell.com/Files/en-us/Html/Manuals/T640/System%20memory=GUID-0BD53828-BAAC-4017-B9BC-7B67DC6B7AEF=2=en-us=.html)
