# ARP - DNS

```bash
ip -c a s
... mtu 1500    #Dataframe size (datalayer - ethernet)

arp -c
ip neighbor

arp -d <ip>     #clear arp cache

# yum install bind-utils
dig www.pluralsight.com     #DNS lookup utility
host www.pluralsight.com
```

```bash
ping -c 1 -s 1473 192.168.1.1       #-s: size of the dataframe - data layer (ethernet mut size: ip -c a s to check its value)
ping -t 5 -c 1 4.2.2.2              #-t: change TTL Value
```

```bash
#yum install traceroute
traceroute -n 4.2.2.2   #-n no dns lookup
 1  192.168.2.1  1.168 ms  1.356 ms  1.705 ms
 2  10.11.17.17  9.279 ms  11.926 ms  21.641 ms
 3  10.178.206.128  6.893 ms  7.155 ms  7.116 ms
 4  10.178.206.129  7.334 ms  8.574 ms  8.653 ms
 5  64.230.91.52  16.339 ms 64.230.91.50  24.574 ms  27.589 ms
 6  64.230.79.142  20.757 ms 64.230.79.148  16.487 ms 64.230.79.142  20.047 ms
 7  64.230.78.181  18.601 ms  15.199 ms 64.230.78.183  17.310 ms
 8  4.30.180.57  16.652 ms  16.586 ms  16.173 ms
 9  * * *       #not configured to reply to TTL request (aka traceroute mechanism )
10  4.2.2.2  15.936 ms  16.836 ms  15.362 ms
```

> Quagga: linux package to do dynamic routing

```bash
ip route add 192.168.1.1/32 via 192.168.2.1 dev enp0s3  #add route to ip/32 (just this one) via <ip2> device <interface>
ip route del 192.168.1.1

ip route add 192.168.1.0/24 via 192.168.2.1 dev enp0s3 #add route to the network <ip>/mask

#for permanent
#/etc/sysconfig/network-scripts/route-enp0s3
> ip route add 192.168.1.0/24 via 192.168.2.1 dev enp0s3

# Need a 'default' route for unknown network
#/etc/sysconfig/network-scripts/ifcfg-enp0s3
> GATEWAY=192.168.2.1           #Will add a default route for unknow traffic

ip route

```

## Network

```bash
nmap -sn 192.168.2.0/24         #<network/mask>
nmap 192.168.2.0/24             #deep scan

```

## TCP

```bash
#check ports listen
ss -ltn -4      #listenning tcp socket ipvn4
ss -t           #established connection
netstat -an | head

falloc -l 1GB largeFile.bin

tc qudisc add dev enp0s8 root netem delay 3000ms loss 5%

```

## Troubleshooting

```bash
scp root@192.168.1.1:/var/www/html/largeFile.bin
#Network unreachable
ping 192.168.1.1
#Network unreachable
ip route
# default route ?
arp -a
# empty ?

# Test physical connectivity of network link / up or down / port speed / duplex
ethtool <interface>
> Link detected : no ???

# Test  layer2 to layer3 mapping
arp 

# Test ip connectivity
ip
route
ping

# Tcp connection / port
netstat
ss

# Wireshark

```

## Security

### Logging

```bash
rpmquery --all | grep rsyslog
systemtl status rsyslog
#/etc/ryslog.conf
```

## Ressources

https://app.pluralsight.com/library/courses/linux-networking-advanced-lfce/table-of-contents
http://amzn.to/29X7dyT

- The TCP/IP set by Comer + TCP/IP Illustrated set by Richard Stevens
- The TCP/IP Guide by Charles M. Kozierok
- Unix Network Programming" by Stevens
- Understanding Linux Network Internals" by Benvenuti
- The Linux Programming Interface: A Linux and UNIX System Programming..." by Kerrisk
