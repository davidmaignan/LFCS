# Essentials commands

## kernel info

```bash
cat /etc/system-release
lsb_release -d
rpm -qf $(which lsb_release)
uname -r
cat /proc/version

# ubuntu
lsb_release -d
cat /etc/issue
```

## Boot partition

```bash
cat /proc/cmdline
df
```

## Partitions

```bash
lsblk
fdisk -l
```

## Disk - Space -

```bash
du -sh /etc (disk usage)
```

## Messassing users logged

```bash
write david
wall < file_message.txt

mesg y|n (turn on|off messaging)
```

## System

```bash
shutdown
reboot
halt
poweroff (forced)
init --help (0: poweroff, 6: reboot....)

shutdown -h 10 (minutes) "The system goes down in 10 minutes"
shutdown -c (cancel)
```

## NetWorking

```bash
ip -brief addr / link
ip addr (ip a)
ip r (root)
ip n (list neighbourgs))

ip netns (namespace - virtualize namespacing))
```

## Process management

```bash
ps
pgrep
```

## Runlevels: defines level to log/run system (5: gui, 3: multi-agent)

```bash
who
who -r (runlevel: 5 gui)
runlevel (get level)
systemctl get-default
systemct set-default multi-user.target
systemctl isolate multi-user.target (change run level)
ex: runlevel: 5 (gui), 3 (multi-user)

init: 0 (poweroff), 6 (reboot)
```

## add user to sudoers
usermod -aG wheel david (add david to sudoer)

## Proc
cat /proc/version

## Piping and redirection
cut -f7 -d: /etc/passwd | sort | uniq | wc -l
mkfifo mypipe
ls > mkfifo


## tar
```bash
tar -cvf /tmp/david.tar /home/david
tar -tf david.tar (check content)
tar -cvzf (create/verbose/gzip/format)
tar -cvjf (create/verbose/bzip2/format)
(-c create / -t test / -x expand (in pwd))
```

#### cpio

```bash
find -name '*.pdf' | cpio -0 > /tmp/pdf.cpio
cpio -id < /tmp/pdf.cpio (i: input, d:directory)
```

## tree
display file structure

## dd 
gzip file.tar / gunzip file.tar.zip
bzip2 file.tar / bunzip2 file.tar.bz2

## Versioning
ci hello.sh
rlog -b hello.sh
co -l hello.sh

## Permission
stat hello.sh,v
umask u=rwx,g=rw,0=
umask: remove permissions
chmod 467 file1
chmod u=r,g=rw,o=rwx file1
chmod a+x file2
chmod a+rw file2
chmod go= file2
chgrp wheel file
newgrp wheel
id -gn
cp -a file2 /root/file2na
su -l (set all ENV)

/etc/sudoers (SSHPermitRootLogin)


## screen
screen command to study


## Boot process
vi /etc/default/grub 
grub2-mkconfig -o /boot/grub2/grub.cfg

## Change hostname
hostnamectl set-hostname hostname.domain.com


## Networking
ip -brief addr
nmcli conn show
nmcli conn up enp0s2
> set ONBOOT=yes to /etc/sysconfig/network-scripts/ifcfg-enp0s3




