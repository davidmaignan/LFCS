# Tar

```bash
## Create archive
tar -cf doc.tar /usr/share/doc
tar --list --file=doc.tar

tar -xvf <tar_file.tar> -C <directory_target>

tar -cjfv doc.tar /usr/share/doc

gzip, gunzip

```

## CPIO - create archive
```bash
find -name '*.pdf' | cpio -o > /tmp/pdf.cpio

```

## imaging with dd (duplicate disk)
```
dd if=/source of=/destination
dd if=/dev/sda of=sda.mbr count=1 bs=512
```

##fdisk
```bash
fdisk -l
```