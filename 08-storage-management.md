
# Storage Management

## Introduction
👋 In this section, we will explore how to manage disks, partitions, LVM, and Stratis in a Red Hat Linux environment.

## Disks & Partitions:
<p align="center">
  <img src="images/Cap1.JPG" alt="cap" style="width: 600px;"/>
</p> 

### Theory:

- **Partitioning**: creating one or more independent storage zones.
- **MBR Disk Structure**: maximum 4 partitions: 4 primary or 3 primary + 1 extended (which can contain multiple logical).
- Example:
  - SATA device:
    - `/dev/sda`: first SATA disk.
    - `/dev/sdb`: second SATA disk.
    - `/dev/sda1`: first partition of the first disk.
    - `/dev/sda2`: second partition of the first disk.
- **File System Types**: the way Linux organizes and stores its files on a device: ext2, ext3, ext4, jfs, xfs...
- **Mounting**: integrates a file system into the directory tree; once mounted, you can navigate its files like any other directory.
- **SWAP partition**: a temporary space on disk used to move inactive data from RAM.

### Commands:
- `lsblk` → view disks and partitions
- `fdisk /dev/disk` then `n` → create a partition
- `mkfs.filesystem_type /dev/partition` → format the partition
- `mkdir /mount_point` → create the mount point
- `blkid /dev/partition` → get UUID
- Add to `/etc/fstab`:
  ```
  UUID=<uuid>  /mount_dir  xfs  defaults  0 0
  ```
- `mount -a` → mount all entries from `/etc/fstab`
- `umount /partition`
- `fdisk /dev/disk` then `d`, then `w` → delete the partition

### SWAP Partition:
- `fdisk /dev/disk` → create partition, then `t`, then type `82`
- `mkswap /dev/partition`
- `free -m` → verify
- Add to `/etc/fstab`:
  ```
  UUID=<uuid> none swap defaults 0 0
  ```
- `swapon -a` → activate
- `swapoff -a` → deactivate

## Lab 07

### Q0. Create a new partition `/dev/sdb1` with 500MB  
### Q1. Format it as ext3  
### Q2. Mount it at `/mnt` during startup

```bash
fdisk /dev/sdb …
mkfs.ext3 /dev/sdb1
echo “UUID=uuid  /mnt  ext3  defaults 0 0” >> /etc/fstab
mount -a
```

### Q3. Create and activate a 1GB SWAP partition (`/dev/sdb2`) without affecting existing SWAP

```bash
fdisk /dev/sdb …
mkswap /dev/sdb2
echo “UUID=uuid  none  swap  0 0” >> /etc/fstab
swapon -a
```

### Q4. Create a 2GB SWAP partition active at boot

(Same steps as above)

## Logical Volume Management (LVM)

### Theory:
<p align="center">
  <img src="images/Ca.JPG" alt="cap" style="width: 600px;"/>
</p> 
To create a logical volume:
- Create a physical volume (PV) from a partition.
- Create a volume group (VG) from PVs.
- Create a logical volume (LV).

> Why? Need an 8G volume but only small partitions? → LVM.

### Commands:

```bash
pvcreate /dev/partition
vgcreate vgname /dev/partition1 /dev/partition2
lvcreate -L size -n lvname vgname
mkfs.xfs /dev/vgname/lvname
echo “/dev/vgname/lvname  /mount_point  xfs  defaults 0 0” >> /etc/fstab
mount -a
```

### Extend Logical Volume:
**If VG has free space:**
```bash
lvextend -r -L +<size> /dev/vgname/lvname
```

**If VG doesn't have free space:**
```bash
pvcreate /dev/newpartition
vgextend vgname /dev/newpartition
lvextend -r -L +<size> /dev/vgname/lvname
```

### Using PE count and size:
```bash
vgcreate -s 2M vgname /dev/pv
lvcreate -l 60 -n lvname vgname
```

To delete:
```bash
umount /dev/vg/lv
lvremove /dev/vg/lv
vgremove vg
```

## Lab 08

### Q0. Create a VG of 510MB named `vol0` with PE size 2M. LV `lv0` with 80MB. Mount at `/cms`.

```bash
fdisk /dev/sda → +512M
vgcreate -s 2M vol0 /dev/sda1
lvcreate -L 80M -n lv0 vol0
mkfs.xfs /dev/vol0/lv0
mkdir /cms
echo “/dev/vol0/lv0  /cms  xfs  defaults 0 0” >> /etc/fstab
```

### Q1. Create LV `lvi` with 60 extents in VG `vgi` (PE=16M), mount on `/record` as ext3

```bash
vgcreate -s 16M vgi /dev/sda2
lvcreate -l 60 -n lvi vgi
mkfs.ext3 /dev/vgi/lvi
mkdir /record
echo “/dev/vgi/lvi  /record  ext3  defaults 0 0” >> /etc/fstab
mount -a
```

### Q2. Resize LV `lv0` to 300MB

```bash
lvextend -L +148M /dev/vg/lv0
```

## Advanced Storage with Stratis

### Theory:

- Combine multiple block devices into a single storage pool.
- Create thin-provisioned filesystems from the pool.
- Snapshots supported.

### Commands:

```bash
dnf install stratis-cli stratisd
systemctl enable --now stratisd
wipefs -a /dev/sda
stratis pool create poolname /dev/sda
stratis filesystem create poolname fsname
blkid /dev/stratis/poolname/fsname
echo “UUID=... /mount xfs x-systemd.requires=stratisd.service 0 0” >> /etc/fstab
mount -a
```

To delete:
```bash
umount /dev/stratis/pool/fsname
stratis filesystem destroy pool fsname
stratis pool destroy pool
```

## Lab 03

### Q0. Create 1GB SWAP, persistent

```bash
fdisk /dev/sda → +1G
fdisk → t → 82
mkswap /dev/sda1
echo “UUID=...  none  swap  defaults 0 0” >> /etc/fstab
swapon -a
```

### Q1. LV `lv0_ext4` with 30 extents, VG `vgroups` with 2G size, PE = 64M, mount on `/home/lv0`

```bash
fdisk /dev/sdb → +2112M
pvcreate /dev/sdb1
vgcreate -s 64M vgroups /dev/sdb1
lvcreate -l 30 -n lv0_ext4 vgroups
mkfs.ext4 /dev/vgroups/lv0_ext4
mkdir /home/lv0
echo “/dev/vgroups/lv0_ext4  /home/lv0  ext4  defaults 0 0” >> /etc/fstab
mount -a
```

### Q2. Create VFAT LV and mount on `/fatVolume`

```bash
mkfs.vfat /dev/vg/lv
mkdir /fatVolume
echo “/dev/vg/lv  /fatVolume  vfat  defaults 0 0” >> /etc/fstab
mount -a
```

### Q3. Create Stratis pool `pool1`, filesystem `fs1`, mount at `/mnt/fs1`, add 1GB file, create snapshot `snap1`, mount it on `/mnt/snap1`

```bash
stratis pool create pool1 /dev/sdb1
stratis filesystem create pool1 fs1
dd if=/dev/zero of=/mnt/fs1/fichier bs=1M count=1024
stratis filesystem snapshot pool1 fs1 snap1
mkdir /mnt/snap1
echo “/dev/stratis/pool1/snap1  /mnt/snap1  xfs  x-systemd.requires=stratisd.service 0 0” >> /etc/fstab
mount -a
```
