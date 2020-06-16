# Storage Management

https://app.pluralsight.com/library/courses/lfcs-linux-storage-management/table-of-contents

## Blocks

SCSI / IDE / SATA : https://getprostorage.com/blog/scsi-vs-sata-vs-ide/

```bash
lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0    8G  0 disk                # fix drive Sci
├─sda1            8:1    0    1G  0 part /boot          # partition
└─sda2            8:2    0    7G  0 part                # partition
  ├─centos-root 253:0    0  6.2G  0 lvm  /              # lvm: logical volume
  └─centos-swap 253:1    0  820M  0 lvm  [SWAP]
sr0              11:0    1 1024M  0 rom                 # CD room
```

Add new disk paritition with virtualbox storage

```bash
lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0    8G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0    7G  0 part
  ├─centos-root 253:0    0  6.2G  0 lvm  /
  └─centos-swap 253:1    0  820M  0 lvm  [SWAP]
sdb               8:16   0    8G  0 disk                # new partition
sr0              11:0    1 1024M  0 rom
```

## Partitionning

List, create, delete, modify storage partitions

|  File system (ext2, ext3, ext4, ReiserFX, XFS, Btrfs, ...)  | |
|---|---|
| Primary Partition (max 4) + Logical partitions (depends of driver -)  |  Partition (max 128) |
| MBR partition (2TB max)   | GUID Partition (8ZB max)  |
|  Disk | |

```bash
fdisk -l /dev/sdb       #list 
```
