# Linux filesystem online resizing

![UbuntuLVM](lvm.jpg)

## Hardware

## Partition

```bash
sudo parted
print all
```
> Model: VMware Virtual disk (scsi)  
Disk **/dev/sda**: 161GB  
Sector size (logical/physical): 512B/512B  
Partition Table: gpt  
Disk Flags:  
Number  Start   End     Size    File system  Name  Flags  
 1      1049kB  2097kB  1049kB                     bios_grub  
 2      2097kB  2150MB  2147MB  ext4  
 3      2150MB  161GB   159GB  

```bash
select /dev/sda
resize part 3 100%
q
```
## Physical Volume

```bash
pvdisplay
```
--- Physical volume ---
   PV Name               /dev/sda3
   VG Name               ubuntu-vg

```bash
pvresize /dev/sda3
```

## VG e LV

```bash
sudo vgdisplay
sudo lvm lvdisplay
```
> --- Logical volume ---  
  LV Path                /dev/ubuntu-vg/ubuntu-lv  
  LV Name                ubuntu-lv  
  VG Name                ubuntu-vg  
  LV UUID                cf6RI7-FNu8-MCnJ-VnRB-bBpD-FrJs-X6qqJ7  
  LV Write Access        read/write  
  LV Creation host, time ubuntu-server, 2022-08-10 20:00:53 +0000  
  LV Status              available  
  `#` open                 1  
  LV Size                <148.00 GiB  
  Current LE             37887  
  Segments               1  
  Allocation             inherit  
  Read ahead sectors     auto  
  `-` currently set to     256  
  Block device           253:0  

```bash
sudo lvm lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

## File System
```bash
df -h
```

>Filesystem                         Size  Used Avail Use% Mounted on  
tmpfs                              1.6G  1.8M  1.6G   1% /run  
/dev/mapper/ubuntu--vg-ubuntu--lv  146G   91G   49G  66% /  
tmpfs                              7.9G     0  7.9G   0% /dev/shm  
tmpfs                              5.0M     0  5.0M   0% /run/lock  
/dev/sda2                          2.0G  254M  1.6G  14% /boot  
tmpfs                              1.6G  4.0K  1.6G   1% /run/user/1001  

**Resizing of mount point /**

```bash
sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```
