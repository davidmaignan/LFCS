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
