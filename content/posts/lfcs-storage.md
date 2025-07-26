+++
draft = false
date = 2025-07-19T18:21:31+02:00
title = "LFCS - Storage"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
series = ["LFCS"]
+++


The last module in my LFCS prep... storage management!

<!--more-->

Installing Arch with LVM earlier this year was amazing, so this is familiar content, good to brush up on the skills anyway.

#### Partitioning

```shell
# windows ntfs
# linux ext4

lsblk # list block devicesj
sda # s is serial , a is first disk
sda1 # s is serial , a is first disk, 1 is first partition

# everything is a file including disks
#sda1 is a file at /dev/sda1
#sda  is at /dev/sda

sudo fdisk --list /dev/sda

# storage is devided into sectors

sudo cfdisk /dev/sdb # easier than fdisk

#gpt guid partition table
#old hardware mbr/dos
```

#### Swap Space

```shell
swapon --show
sudo mkswap /dev/vdb3

sudo dd if=/dev/zero of=/swap bs=1M count=128 status=progress # write zeros to file
sudo chmod 600 /swap
sudo mkswap /swap
sudo swapon --verbose /swap
swapon --show
```

#### File Systems

```shell
# ubuntu uses ext4

sudo mkfs.xfs /dev/sdb1 # formats the file system
sudo mkfs.ext4 /dev/sdb1 # formats the file system (make file system)
man mkfs.ext4
sudo fdisk -L
sudo mkfs.xfs -L "Backup Volume" /dev/sdb1

xfs # tab tab, shows utils

xfs xfs_admin -l /dev/sdb1

sudo mkfs.ext4 /dev/sdb2 
sude tune2fs -L "Second Label" /dev/sdb2

```

#### Mounting

```shell
sudo mount /dev/vdb1 /mnt/
sudo umount /mnt/ 

lsblk

sudo vim /etc/fstab

sudo systemctl daemon-reload

man fstab

sudo blkid /dev/vdb1
```

#### File System and Mount Options

```shell
findmnt # shows all mounted items
findmnt -t xfs,ext4 # type option

sudo mount -o ro /dev/vbd2 /mnt # mount read only option
man mount # for options
man xfs # options for xfs



```

#### Remote FileSystems - NFS

```shell
# NFS - network file system
# NFS server and client

# server config

sudo apt install nfs-kernel-server
sudo vim /etc/exports
man exports

# eg share with any client
/etc *(ro,sync,no_subtree_check)
/etc *.domain.com(ro,sync,no_subtree_check) # only clients with this subdomain

sudo exportfs -r # reexport changes
sudo exportfs -v

# client config
# mounting remote file system

sudo apt install nfs-common
sudo mount ip_or_host_name:/path/to/remote /path/to/local
sudo mount 127.0.0.0:/etc /mnt
sudo umount /mnt
# can add to fstab
```

#### Block Devices  - NBD

```shell
# NBD - Network Block Device
# adds a block device that references remote device

# NBD server
sudo apt install nbd-server
sudo vim /etc/ndb-server/config # configure NBD devices
sudo systemctl restart ndb-server.service
man 5 ndb-server # config help

# NBD client
sudo apt install ndb-client
sudo modprobe nbd # loads kernel module
sudo vim /etc/modules-load.d/modules.conf # to load at boot
sudo nbd-client 192.168.1.8 -N partition2 # partition name as setup on server, hostname or ip
sudo mount /dev/ndb0 /mnt

lsblk

sudo nbd-client -d /dev/nbd0 # disconnect
sudo ndb-client -l 192.168.1.8 # list remote available partitions


```

#### Managing LVM Storage

```shell
# Logical Volume Manager
# Makes changing partition sizes possible
sudo apt install lvm2

# PV  - Physical Volumes (real disks)
sudo lvmdiskscan
sudo pvcreate /dev/sdc /dev/sdd
sudo pvs # shows sizes

# VG - Volume group
sudo vgcreate my_volume /dev/sdc /dev/sdd # 2 seperate disk that can be seen as one disk, can expand disk without turning off server

sudo vgextend my_volume /dev/sde
sudo vgs
sudo vgreduce my_volume /dev/sde

# LV - Logical volume
sudo lvcreate --size 2G --name partition1 my_volume 
sudo vgs # inspects volume groups

sudo lvs # lists logical volumes

# PE - Physical extent
sudo lvresize --extents 100%VG my_volume/partition1
sudo lvs # makes it easy to resize voulems
sudo lvresize --size 2G my_volume/partition1

sudo lvdisplay

# create ext4
sudo mkfs.ext4 /dev/my_volume/partition1

sudo lvresize --resizefs --size 3G my_volume/partition1 # resizes the LV and the file system

man lvm
```

#### Monitoring Storage Performance

```shell
sudo apt install sysstat

iostat # I/O statistics
pidstat # process id statistics

iostat # historical usage since boot (not current)
# tps (transfers per second)
# kb_read
# kb_wrtn
# processes can starve storage

# to test we are going to write a lot of small data to the disk
# if input file
# of output file
# bs = 1 (1 byte)
# count how many times to write
# this command will write for a long time to disk (lots of tps)
# oflag (no caching)
dd if=/dev/zero of=DELETEME bs=1 count=1000000 oflag=dysnc & # writes data to 

iostat 1 # refreshes every 1 second

```

iostat:
![[Pasted image 20250714055216.png]]

```shell
# determine the process writing

pidstat -d # devices
pidstat -d 1 # every one second

sudo dmsetup info /dev/dm-0

lsblk
ps 1411 # the dd command
kill 1411

iostat -h # human readable format
pidstat --human

iostat -p ALL # all partitions
iostat -p sda # all on sda device
man iostat
man pidstat
```

#### Manage File System Permissions

```shell
# acl (access control lists)
# specific permissions for 2 or more users

sudo apt install acl

sudo setfacl --modify user:matt:rw file1 # file specific permissions

# most files dont have acls
getfacl file1
sudo setfacl --modify mask:r file1 # limits permissions
sudo setfacl --modify group:sudo:rw file1
sudo setfacl --modify user:matt:--- file1 # remove all permissions for a user
sudo setfacl --remove user:matt file1 # remove permissions
sudo setfacl --remove group:sudo file1 # remove permissions for group
sudo setfacl --remove-all file1 # remove all acl for a file

sudo setfacl --recursive -m user:matt:rwx dir1/ # recursive give acl to directory

# file attributes
sudo chattr +a file1 # can only append data to a file not remove
sudo chattr -a file1 # remove append attribute 
sudo chattr +i file1 # immutable (file cant be change in any way)
lsattr file1
lsattr 
sudo chattr -i file1 # remove immutable file attribute
man chattr
```

And thats it! Ive completed all my notes for the LFCS exam that I will be taking soon. Linux is essential in any DevOps/SRE role, so its an important skill to master.
