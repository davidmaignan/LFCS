# Installation

```bash
# check terminal connected to
tty
ls -l $(tty)
```

VirtualBox config
Nat Network: get different ip for each vm
Host-only adaptor : to connect back to vm
> DHCP server on (tools > properties)

## Networking

```bash
ip a s

### Show connection and activate them on bot
nmcli conn show
nmcli conn up <con name>

sed -i s/ONBOOT=no/ONBOOT=yes/ /etc/sysconfig/network-scripts/<con name>
```

## Installing X

```bash
yum install -y redhat-lsb-core net-tools epel-release kernel-headers kernel-devel
yum groupinstall -y "Development Tools"
yum groupinstall -y "X Window System" "MATE Desktop"

systemctl set-default graphical.target
systemctl isolate graphical.target

```

https://app.pluralsight.com/library/courses/lfcs-red-hat-7-essentials/table-of-contents

