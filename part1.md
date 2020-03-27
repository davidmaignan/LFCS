# Installation

```bash
# check terminal connected to
tty
ls -l $(tty)
```

VirtualBox config
Nat Network: get different ip for each vm
Host-only adaptor : to connect back to vm

Networking
```bash
ip a s

# Show connection and activate
nmcli conn show
nmcli conn up <con name>

sed -i s/ONBOOT=no/ONBOOT=yes/ /etc/sysconfig/network-scripts/<con name>
```
