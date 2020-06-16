# Centos Enterprise Linux 7 Operations Essentials

https://app.pluralsight.com/library/courses/lfcs-linux-operation-essentials/table-of-contents

## Boot, reboot, shutdown, runlevels, boot process

### Messaging user

```bash
#Send message to a specific user
write <user>
> this is a message

# message to everyone
cat > message
This is a message

# Send message to all logged in users
wall < message
```

### Shutdown commandes

```bash
 reboot
 poweroff
 shutdown -h 10 "The system goes home in 10 minutes"    #send a wall message
 shutdown -c    #cancel the shutdown
 init --help
 telinit --help

 #/run/nologin file created when shutdown is 5 min to happen nobody can login except root
```

### Runlevels & setting default

```bash
who -r      #show run level: 5 (graphical.target) - 2,3,4 (multi-user.target)
systemct get-default
systemctl set-default {graphical|multi-user|rescue}.target
systemctl isolate graphical.target
systemctl isolate rescue.target
```

> ESC in GRUG
> e to edit
> search for linux16 and ctrl + e
> Append systemd.unit=rescue.target
> ctrl + X to resume the boot process

### Boot process

```bash
#/etc/default/grub
GRUB_DISABLE_RECOVERY="false"

#Regenerate grub config
grub2-mkconfig -o /boot/grub2/grub.cfg
```

### Recover lost password

> ESC in GRUB
> e to edit
> search for linux16 and ctrl + e
> remove quiet rhgb add rd.break enforcing=0

```bash
[switch_root:/\#] mount -o remount, rw /sysroot ; chroot /sysroot
[sh-4.2#] passwd ; exit
[switchroot:/\#] mount -o remount, r /sysroot ; exit

> Boot to initramfs - NOTE: default runlevel with SELinux with a permissive state

#login then
restorecon /etc/shadow
setenforce 1

```

### Grub2

```bash
grub2-install /dev/sda
yum reinstall grub2-efi shim    #EFI system

#/etc/default/grub

#Regenerate grub config
grub2-mkconfig -o /boot/grub2/grub.cfg

#Making persistent changes with grubby
grubby --default-kernel
grubby --set-default /boot/vmlinuz-3.1...
grubby --info=ALL
grubby --info /boot/vmlinuz...
grubby --remove-args="rhgb quiet" --update-kernel /boot/vmlinu...


cp /etc/grub.d/01_users .
#!/bin/sh -e
cat << EOF
    set superusers="david"  #not unix account - grub account
    password david camper
EOF

grub2-mkpasswd-pbkdf2   #generate a password encrypted for grub2


```

Kernel entry: linux16 line
Ramdisk : initrd16

```bash
#/etc/grub.d/40_custom
menuentry 'Centos OS Custom' {
    insmod gzio             #load module to open zip file
    insmod part_msdos       #load module for mbr module to access partition msdox
    insmod xfs              #drive for xfs file system
    set root='hd0,msdos1'   #set root file system for grub boot loader 
    linux16 /vmlinuz-3.10.0-1062.el7.x86_64 root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.em.lv=centos/swap        #kernel entry
    initrd16 /initramfs-3.10.0-1062.el7.x86_64.img  #ramdisk entry
    # ... and more if necessary
}

#Regenerate grub config
grub2-mkconfig -o /boot/grub2/grub.cfg

```

### Linux Processes

```bash
ps -e       #all processes
ps -ejH     #tree processes
ps -f       #full
ps -F       #extra full
ps -l       #long listing

ps -elf     #UNIX style
ps aux      #BSD style

pstree

#/proc
ls              #list all processes in proc directory
ps -p1 -f       #full despcription of process 1

cd /proc/$$/    #current process id
echo $$         #processId of current process
ps -p $$ -F

cd $$
ls -l cwd   #/proc/151
ls -l exe   #/usr/bin/bash

stty -a     #get list of shortcuts to stop, kill, erase, ... processes

kill -l             #list kill signals
kill -15 <pid>       #signal terminate
kill -term <id>
kill -9 <id>        #signal kill 

pgrep sshd      #grep process
ps -F -p $(pgreg sshd)

sleep 100 &
pgrep sleep

top     #sorted by cpu usage
# press f then (narrow up/down) and press s (select) ESC to exit - q (quit)q

jobs
& 
^z bg (bg current process)
fg / bg id
kill %1

#set priority
nice -n 19 sleep 1000   #nice value -20 to +19
nice -n 5 firefox &

renice -n 10 -p 14721

#/etc/security/limit.conf
<user> - priority 10
```

### Monitoring Linux performance

procps-ng package

```bash
rpm -ql procps-ng           #what's included in that package
rpm -qf /usr/bin/top        #query file to see package it belongs

rpm -ql procps-ng | grep '^/usr/bin/'        #get binaries avail in package

free -

pgrep ssh
echo $$                 #get current pid
pmap <pid>              #show memory map (process, shared lib, ..)
pwdx $(pgrep sshd)      #get working directory for a process
cd /proc/$$



uptime  
> 16:54:30 up 54 min,  2 users,  load average: 0.01, 0.02, 0.03

who
> root     tty1         2020-04-13 16:03
> root     pts/0        2020-04-13 16:06 (192.168.56.1)

w
> 16:55:58 up 56 min,  2 users,  load average: 0.00, 0.01, 0.03
> USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
> root     tty1                      16:03   52:54   0.01s  0.01s -bash
> root     pts/0    192.168.56.1     16:06    6.00s  0.13s  0.00s w
> [1]+  Done                    nice -n 19 sleep 2000


lscpu   # display information about the CPU architecture

cat /proc/uptime
> 3837.08 3814.13     #col1: system up in seconds - col2: time idle
cat /proc/loadavg
> 0.00 0.01 0.03 1/118 2078


watch -n 4 uptime
tload 

top -b -n1
vmstat -S {K|k|m}   #K: ilobytes, ..

procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0 1395224 1424949  85463 1694146    1    9    90    97   26   19 22  9 68  0  0
```

### Performance - Systat

```bash
yum install -y sysstat

cat /etc/cron.d/sysstat         #jobs to collect sysstat data
cat /etc/sysconfig/sysstat      #config for sysstat (logging, compression, ...)
systemctl enable sysstat
rpm -ql sysstat | grep '^/usr/bin/'

iostat -m 5 3           #IO activity: m:mega, 5: interval, 3: count
pipstat -p <pid>  5 3   #Process activity.
mpstat -p ALL 2 3       #All processes


sar -u                  #Reporting activity about server
10:25:58 AM       LINUX RESTART

10:30:02 AM     CPU     %user     %nice   %system   %iowait    %steal     %idle
10:40:01 AM     all      0.01      0.00      0.07      0.00      0.00     99.92
10:50:01 AM     all      0.03      0.00      0.26      0.02      0.00     99.69
Average:        all      0.02      0.00      0.17      0.01      0.00     99.81

sar -r      #memory usage
sar -b      #disk io
sar -n DEV  #Network card activity
sar -q      #Load average (processes, blocked )

cd /var/log/sa      #log for system activity

sar -s 14:50:00 -e 15:10:00 -f /var/log/sa/<sa_file>

```

### Managing shared librairies

```bash
ldd /usr/bin/ls
	linux-vdso.so.1 =>  (0x00007ffdbc13e000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fe0afc0e000)
	libcap.so.2 => /lib64/libcap.so.2 (0x00007fe0afa09000)
	libacl.so.1 => /lib64/libacl.so.1 (0x00007fe0af800000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fe0af432000)
	libpcre.so.1 => /lib64/libpcre.so.1 (0x00007fe0af1d0000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fe0aefcc000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fe0afe35000)
	libattr.so.1 => /lib64/libattr.so.1 (0x00007fe0aedc7000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fe0aebab000)

pmap $$
ldd /usr/bin/bash


#/lib -> usr/lib (usr: unix system resources)
#/lib64
#/etc/ld.so.conf.d/ (more config for libs)

ldconfig -p             #show cache librairies
ldconfig                #reload the cache
ls -l /etc/ld.so.cache
```

### Scheduling task

```bash
systemctl status crond
#/etc/crontab
#/etc/cron.d
crontab -l		#list
crontab -e		#edit
crontab -r		#remove

#min hour d-o-m month dow

/etc/anacrontab
#period delay job-id command

```

### Log files & Logrotate

```bash
#User logins
lastlog | grep -v 'Never'
last -n 10
root     pts/0        192.168.56.1     Thu Apr 16 15:10   still logged in   
root     tty1                          Thu Apr 16 15:09   still logged in   
reboot   system boot  3.10.0-1062.18.1 Thu Apr 16 15:08 - 17:13  (02:04)    
root     pts/0        192.168.56.1     Wed Apr 15 13:56 - crash (1+01:12)   
root     tty1                          Wed Apr 15 13:54 - crash (1+01:14)   
reboot   system boot  3.10.0-1062.18.1 Wed Apr 15 13:54 - 17:13 (1+03:19)   
root     pts/1        192.168.56.1     Wed Apr 15 10:41 - 10:51  (00:10)    
root     pts/0        192.168.56.1     Wed Apr 15 10:39 - crash  (03:14)    
root     pts/0        192.168.56.1     Wed Apr 15 10:21 - 10:38  (00:16)    
root     tty1                          Wed Apr 15 10:21 - crash  (03:32) 

last reboot
last <user>

lastb           #list bad logging

#Root access
grep sudo /var/log/secure*  #List all commands run as sudo
grep su: /var/log/secure*   #List session open but NOT THE COMMAND


#Extract with AWK
awk '/sudo/ {print $5,$6,$14 }' /var/log/secure

> secure.sh
#!/usr/bin/bash
awk "/$1/ {print \$5, \$6, \$14 }" $2


#/etc/rsyslog.conf
local1.info /var/log/andrew

#Test
logger -p local1.warn "Test message"

#Check
tail /var/log/messages
tail /var/log/andrew

#logrotate
/etc/logrotate.conf 
/etc/cron.daily/
#add a rotation for a log file in logrotate.conf
/var/log/andrew {
   missingok
   notifempty
   size 10
   compress
}

logrotate /etc/logrorate.conf


# Log from memory
journalctl -n 50     #last 50 entries
journalctl -f
journalctl -b

mkdir /var/log/journal
vi /etc/systemd/journald.conf

journalctl -u sshd.service
journalctl --since "2020-04-15 12:00:00"
journalctl --since "10 minutes ago"

```

### SELinux

```bash
#enforcing=0	/changing mode to permissive in boot loader

ls -Z <file>
ps -Z <pid>
id -Z

ls -Z /etc/shadow  
#                     user:role:type(context):mls
----------. root root system_u:object_r:shadow_t:s0    /etc/shadow

getenfore  #check mode
sestatus   #SELinux status
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      31

#/etc/selinux/config
SELINUX={enforcing|permissive|disabled}
SELINUXTYPE=targeted

setenforce 0 | 1 # permissive | Enforcing
getenforce


 ps -Zp $(pgrep sshd)
LABEL                             PID TTY      STAT   TIME COMMAND
system_u:system_r:sshd_t:s0-s0:c0.c1023 1208 ? Ss     0:00 /usr/sbin/sshd -D
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2174 ? Ss   0:00 sshd: root@pts/0

#Audit logs SELinux commands
#/var/log/audit/audit.log
ausearch -m avc
ausearch -m avc -ts recent

restorecon /etc/shadow
ls -Z /etc/shadow
semanage fcontext -l

#SELinux boolean

getsebool -a

getsebool httpd_read_user_content
setsebool httpd_read_user_content on        #change session / memory only
setsebool -P httpd_read_user_content on     #permenantly
#/etc/selinux/targeted/polidy

semanage port -l | grep ssh
semanage port -a -t ssh_port_t -p 2222

```

### Managing software on Centos

```bash
rpm -qa             #list all same than: yum list installed
rpm -i <name.rpm>   #install rpm (attention dependencies)
rpm -e nmap
rpm -ql nmap            #query list
rpm -qpl <package.rpm>  #query from rpm file
rpm -V nmap             # 


rpm -i httpd.rpm        #dependancies
error: Failed dependencies:
	httpd-tools = 2.4.43-1.fc31 is needed by httpd-2.4.43-1.fc31.x86_64
	mod_http2 is needed by httpd-2.4.43-1.fc31.x86_64
	system-logos-httpd is needed by httpd-2.4.43-1.fc31.x86_64

rpm -e nmap                     #remove package
rpm -qf /etc/hosts              #get package from
> setup-2.13.6-1.fc31.noarch
rpm -qi setup                   #query rpm package instaled
rpm -V setup                    #verification modification

yum version
yum install --downloadonly httpd
yum info 
yum install -y nmap
yum deplist nmap
yum list installed
yum list available

#configure yum repositories

#/etc/yum.repos.d
yum info epel-release.noarch
rpm -ql epel-release


yum repolist
yum repolist all    #show disabled one
yum-config-manager --add <repo_id>
yum-config-manager --enable <repo_id>

dnf repolist
dnf config-manager --disable <repo_id>
dnf makecache

# yum cache
yum makecache   #build cache from repositories
yum clean {all|cache|dbcache|..}

# Kernel update
yum update kernel       #install the new kernel (multiple kernel)

#/etc/yum.conf
> exclude=kernel*

# Source RPMS
grep ^enabled CentOS-Sources.repo
yum repolist
yum install -y yum-utils
yumdownloader --source zsh      #download rpm
yum install ncurses-devel
rpm -i zsh-....src.rpm
cd /root/rpmbuild/SOURCES/
tar -xj zsh.tar.bz2
cd zsh/ ; ./configure     #create makefile
make ; make install


```

### Services

```bash
systemctl enabled httpd --now       #enable and start
systemctl {enabled|disabled|start|stop|restart} <service>
systemctl list-units --type=service
systemctl list-unit-files

systemctl mask name.service
systemctl unmask name.service
```

### Puppet

```bash
yum install puppet
puppet --version
facter | grep hostname

```
