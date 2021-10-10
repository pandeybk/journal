# Oct 9 2021

# Issue 1
https://ubuntu.com/server/docs/network-configuration

```
sudo ip addr add 192.168.4.8/24 dev eno3
```

The ip can then be used to set the link up or down.
```
ip link set dev eno3 up
ip link set dev eno3 down
```

To verify the IP address configuration of enp0s25, you can use the ip command in the following manner.

```
ip address show dev eno3

2: eno3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 24:6e:96:30:df:fc brd ff:ff:ff:ff:ff:ff
    inet 192.168.4.8/24 scope global eno3
       valid_lft forever preferred_lft forever
    inet6 fe80::266e:96ff:fe30:dffc/64 scope link 
       valid_lft forever preferred_lft forever
goglides@goglides:~$ 

```

To configure a default gateway, you can use the ip command in the following manner. Modify the default gateway address to match your network requirements.

```
sudo ip route add default via 192.168.4.1
```

To verify your default gateway configuration, you can use the ip command in the following manner.

```
ip route show
default via 192.168.4.1 dev eno3 
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown 
192.168.4.0/24 dev eno3 proto kernel scope link src 192.168.4.8 
```


# Issue 2
```
# netplan apply
bind: Address already in use
netplan: fatal error: cannot bind to port 2983, is another daemon running?, exiting.
```

Solutions:
you've probably confused the netplan calendaring tool with netplan the network configuration tool
install netplan.io not netplan
(sudo) apt install netplan.io netplan-

After that save netplan config on /etc/netplan

```
cat /etc/netplan/01-network-manager-all.yaml 
# Let NetworkManager manage all devices on this system
#
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    eno3:
      dhcp4: no
      addresses:
        - 192.168.4.8/32
      gateway4: 192.168.4.1
      nameservers:
        addresses: [8.8.8.8,4.4.4.4]
```

```
netplan apply
```

References:
https://ubuntuforums.org/showthread.php?t=2400673