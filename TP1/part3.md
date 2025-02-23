# Part III : Storage is still disks in 2025

## Index

- [Part III : Storage is still disks in 2025](#part-iii--storage-is-still-disks-in-2025)
  - [Index](#index)
  - [1. LVM](#1-lvm)
  - [2. HELP my partition is full](#2-help-my-partition-is-full)
  - [3. Prepare another partition](#3-prepare-another-partition)

## 1. LVM

🌞 **Afficher l'état actuel de LVM**

```bash
[root@node1 ~]# pvdisplay
  --- Physical volume ---
  PV Name               /dev/nvme0n1p3
  VG Name               rl
  PV Size               <21.01 GiB / not usable 4.00 MiB
  Allocatable           yes 
  PE Size               4.00 MiB
  Total PE              5377
  Free PE               1
  Allocated PE          5376
  PV UUID               wqVDBY-2Vfu-Zd32-ojeH-1dVy-pz5B-rilChK

[root@node1 ~]# vgdisplay
  --- Volume group ---
  VG Name               rl
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  5
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                4
  Open LV               4
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               21.00 GiB
  PE Size               4.00 MiB
  Total PE              5377
  Alloc PE / Size       5376 / 21.00 GiB
  Free  PE / Size       1 / 4.00 MiB
  VG UUID               QF3KXR-d8yZ-5hBU-4SHC-0klx-PATk-IUoWfr

[root@node1 ~]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/rl/var
  LV Name                var
  VG Name                rl
  LV UUID                PDAERi-gyzN-iO0w-2Ysw-WRPO-5ZlU-f5WaX4
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2025-02-18 14:36:45 +0100
  LV Status              available
  # open                 1
  LV Size                5.00 GiB
  Current LE             1280
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:2
   
  --- Logical volume ---
  LV Path                /dev/rl/root
  LV Name                root
  VG Name                rl
  LV UUID                K59Waq-8ySt-PEeX-Pal5-EChr-QirU-3u9GMv
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2025-02-18 14:36:45 +0100
  LV Status              available
  # open                 1
  LV Size                10.00 GiB
  Current LE             2560
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/rl/home
  LV Name                home
  VG Name                rl
  LV UUID                52qi9y-zE3m-ew9R-Mr8t-dbhB-desF-tQU16G
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2025-02-18 14:36:46 +0100
  LV Status              available
  # open                 1
  LV Size                5.00 GiB
  Current LE             1280
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:3
```

🌞 **Déterminer le type de système de fichiers**

```bash
[root@node1 ~]# blkid /dev/rl/root 
/dev/rl/root: UUID="74e9473b-e9c4-4c3a-9c62-67eeb22ed497" TYPE="ext4"
[root@node1 ~]# blkid /dev/rl/home
/dev/rl/home: UUID="07bcff5e-7564-48bf-896b-bfa2dddf80e2" TYPE="ext4"
```

## 2. HELP my partition is full

🌞 **Remplissez votre partition `/home`**

```bash
dd if=/dev/zero of=/home/dodo/bigfile bs=4M count=2500
```

🌞 **Constater que la partition est pleine**

```bash
[root@node1 ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             4.0M     0  4.0M   0% /dev
tmpfs                317M     0  317M   0% /dev/shm
tmpfs                127M  3.5M  124M   3% /run
efivarfs             256K   33K  224K  13% /sys/firmware/efi/efivars
/dev/mapper/rl-root  9.8G  1.3G  8.0G  14% /
/dev/nvme0n1p2       974M  247M  660M  28% /boot
/dev/mapper/rl-home  4.9G  4.9G     0 100% /home
/dev/mapper/rl-var   4.9G  141M  4.5G   4% /var
/dev/nvme0n1p1      1022M  7.3M 1015M   1% /boot/efi
tmpfs                 64M     0   64M   0% /run/user/0
```


🌞 **Agrandir la partition**

> Allocation d'environ 7G supplémentaire à nvme0n1.

```bash
[root@node1 ~]# fdisk /dev/nvme0n1
[...]
Command (m for help): F

Unpartitioned space /dev/nvme0n1: 6.99 GiB, 7506738688 bytes, 14661599 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes


[root@node1 ~]# fdisk -l /dev/nvme0n1
Disk /dev/nvme0n1: 30 GiB, 32212254720 bytes, 62914560 sectors
Disk model: VMware Virtual NVMe Disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: AB4F8481-21AF-4559-9538-E39B3F3B391E

Device           Start      End  Sectors Size Type
/dev/nvme0n1p1    2048  2099199  2097152   1G EFI System
/dev/nvme0n1p2 2099200  4196351  2097152   1G Linux filesystem
/dev/nvme0n1p3 4196352 48252927 44056576  21G Linux LVM

[root@node1 ~]# parted /dev/nvme0n1
print
Number  Start   End     Size    File system  Name                  Flags
 1      1049kB  1075MB  1074MB  fat32        EFI System Partition  boot, esp
 2      1075MB  2149MB  1074MB  ext4
 3      2149MB  24.7GB  22.6GB                                     lvm

resizepart 3 100%
quit

[root@node1 ~]# lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0          11:0    1  1.6G  0 rom  
nvme0n1     259:0    0   30G  0 disk 
├─nvme0n1p1 259:1    0    1G  0 part /boot/efi
├─nvme0n1p2 259:2    0    1G  0 part /boot
└─nvme0n1p3 259:3    0   28G  0 part 
  ├─rl-root 253:0    0   10G  0 lvm  /
  ├─rl-swap 253:1    0    1G  0 lvm  [SWAP]
  ├─rl-var  253:2    0    5G  0 lvm  /var
  └─rl-home 253:3    0    5G  0 lvm  /home

[root@node1 ~]# pvresize /dev/nvme0n1p3
  Physical volume "/dev/nvme0n1p3" changed
  1 physical volume(s) resized or updated / 0 physical volume(s) not resized

[root@node1 ~]# pvs
  PV             VG Fmt  Attr PSize   PFree
  /dev/nvme0n1p3 rl lvm2 a--  <28.00g 6.99g
```

> Extension d'environ 7G au volume logique rl-home

```bash
[root@node1 ~]# lvextend -l +100%FREE /dev/rl/home

[root@node1 ~]# resize2fs /dev/rl/home
resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/rl/home is mounted on /home; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 2
The filesystem on /dev/rl/home is now 3144704 (4k) blocks long.

[root@node1 ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             4.0M     0  4.0M   0% /dev
tmpfs                317M     0  317M   0% /dev/shm
tmpfs                127M  3.5M  124M   3% /run
efivarfs             256K   33K  224K  13% /sys/firmware/efi/efivars
/dev/mapper/rl-root  9.8G  1.3G  8.0G  14% /
/dev/nvme0n1p2       974M  247M  660M  28% /boot
/dev/mapper/rl-home   12G  4.9G  6.4G  44% /home
/dev/mapper/rl-var   4.9G  141M  4.5G   4% /var
/dev/nvme0n1p1      1022M  7.3M 1015M   1% /boot/efi
tmpfs                 64M     0   64M   0% /run/user/0
```

🌞 **Remplissez votre partition `/home`**

```
dd if=/dev/zero of=/home/dodo/bigfile bs=4M count=3000
```

➜ **Eteignez la VM et ajoutez lui un disque de 40G**

🌞 **Utiliser ce nouveau disque pour étendre la partition `/home` de 20G**

>Avant ->
```bash
[root@node1 ~]# lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0          11:0    1  1.6G  0 rom  
nvme0n1     259:0    0   30G  0 disk 
├─nvme0n1p1 259:1    0    1G  0 part /boot/efi
├─nvme0n1p2 259:2    0    1G  0 part /boot
└─nvme0n1p3 259:3    0   28G  0 part 
  ├─rl-root 253:0    0   10G  0 lvm  /
  ├─rl-swap 253:1    0    1G  0 lvm  [SWAP]
  ├─rl-var  253:2    0    5G  0 lvm  /var
  └─rl-home 253:3    0   12G  0 lvm  /home
nvme0n2     259:4    0   40G  0 disk 
```

>Création du PVS et extension du VG
```bash
[root@node1 ~]# pvcreate /dev/nvme0n2
  Physical volume "/dev/nvme0n2" successfully created.

[root@node1 ~]# vgextend rl /dev/nvme0n2
  Volume group "rl" successfully extended

[root@node1 ~]# lvextend -L+20G /dev/rl/home
  Size of logical volume rl/home changed from <12.00 GiB (3071 extents) to <32.00 GiB (8191 extents).
  Logical volume rl/home successfully resized.

[root@node1 ~]# resize2fs /dev/rl/home
```

>Après ->
```bash
[root@node1 ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             4.0M     0  4.0M   0% /dev
tmpfs                317M     0  317M   0% /dev/shm
tmpfs                127M  3.5M  124M   3% /run
efivarfs             256K   33K  224K  13% /sys/firmware/efi/efivars
/dev/mapper/rl-root  9.8G  1.3G  8.0G  14% /
/dev/nvme0n1p2       974M  247M  660M  28% /boot
/dev/mapper/rl-home   32G   12G   19G  39% /home
/dev/mapper/rl-var   4.9G  141M  4.5G   4% /var
/dev/nvme0n1p1      1022M  7.3M 1015M   1% /boot/efi
tmpfs                 64M     0   64M   0% /run/user/0
```

## 3. Prepare another partition

🌞 **Créez une nouvelle partition**

>Création et formatage
```bash
[root@node1 ~]# lvcreate -L 19G -n web rl
  Logical volume "web" created.

[root@node1 ~]# mkfs.ext4 /dev/rl/web 
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 4980736 4k blocks and 1245184 inodes
Filesystem UUID: 146c9f18-16c2-4068-8acd-16af70261dca
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done 
```

>Montage
```bash
[root@node1 ~]# mkdir /var/www
[root@node1 ~]# mount /dev/rl/web /var/www
[root@node1 ~]# mount
[...]
/dev/mapper/rl-web on /var/www type ext4 (rw,relatime,seclabel)
```

🌞 **Proposez au moins une option de montage**

```bash
[root@node1 ~]# mount -o ro,noexec /dev/rl/web /var/www/
```

>ro = Lecture seulement

>noexec = Empêche l'exécution de binaire

[Part 4](part4.md)