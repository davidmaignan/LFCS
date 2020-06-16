# Get info of system
cat /etc/system-release
lsb_release -d
uname -r
cat /proc/version
cat /proc/cmdline

# IP & IFCONFIG
ifconfig (obsolete)
ip addr
ip r | ip a | ip netns (namespace) 
create network namespace: sudo ip netns netns add development
ip netns (list namespaces)

# Hostname
hostname 
echo $PS1
uname -n

hostnamectl
hostname set-hostname centos.example.com (update /etc/hostname)
hostname centos.example.com (not permanent - only transiant name)

cat /etc/machine-info (check pretty name)
cat /etc/machine-id 

# list boot directory
lsblock
fdisk -l

# DNS client
yum info avahi
getent hosts (list entries in hosts)
grep hosts /etc/nsswitch.conf

cat /etc/resolv.conf (networkmanager add automatically the dns ip found when connecting wifi or ethernet cable ...)

# dig (bind-utils)
dig www.pluralsight.com (query local dns)
dig www.pluralsight.com +short @8.8.8.8 (query remote dns: google one)
dig pluralsight MX (mail exchange server)

# NTP (network time protocol)
-hardwareclock
-system time 

# Network services (service manage network connectivity)
ip -4 a s enp0s8
ip -6 a

systemctl status NetworkManager
nmcli connection
nmcli -p connection show enp0s3
nmcli connection add con-name home ifname enp0s3 type ethernet ip4 192.168.0.99 gw4 192.168.0.1A
nmcli conn up <networkcardname>

systemctl status network

1.Manually set network card
cp /etc/sysconfig/network-scripts/ifcfg-enp0s3 ../ifcfg-enp0s8
- edit manually
mncli con show
mncli con delete WiredConnection1
ifdown enp0s8
ifup enp0s8

systemctl stop NetworkManager.service
systemctl start NetworkManager.service
mncli con show (not showing enp0s8)
systemctl stop NetworkManager.service

# Routing
