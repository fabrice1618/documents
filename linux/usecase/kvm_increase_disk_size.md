# Augmenter taille disque d'une VM KVM

## Stopper la VM

Par exemple :

```bash
$ sudo virsh list
 Id   Name                   State
--------------------------------------
 2    ubuntu22.04 - Docker   running

$ sudo virsh shutdown ubuntu22.04
```

## Modifications de la VM 

- Renommer la VM 'dockerhost'

- Renommer le disque 'dockerhost' :

```bash
$ sudo bash
# cd /var/lib/libvirt/images/
# mv ubuntu22.04.qcow2 dockerhost.qcow2
```

Dans Virtual Machine Manager:

    - menu édition/préférences : activer "enable XML editing"
    - menu détail machine virtuelle / IDE disque 1 -> modifier : <source file="/var/lib/libvirt/images/dockerhost.qcow2"/>

- Augmenter taille du disque

```bash
# sudo qemu-img info /var/lib/libvirt/images/dockerhost.qcow2
image: /var/lib/libvirt/images/dockerhost.qcow2
file format: qcow2
virtual size: 20 GiB (21474836480 bytes)
disk size: 8.67 GiB
cluster_size: 65536
Format specific information:
    compat: 1.1
    lazy refcounts: true
    refcount bits: 16
    corrupt: false

# sudo qemu-img resize /var/lib/libvirt/images/dockerhost.qcow2 +10G

# fdisk -l /var/lib/libvirt/images/dockerhost.qcow2   
Disque /var/lib/libvirt/images/dockerhost.qcow2 : 20 GiB, 21478375424 octets, 41949952 secteurs
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d'E/S (minimale / optimale) : 512 octets / 512 octets

```

## Redemarrer la VM

```bash
$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                               98M  1,3M   96M   2% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  9,8G  8,4G  921M  91% /
tmpfs                              486M     0  486M   0% /dev/shm
tmpfs                              5,0M     0  5,0M   0% /run/lock
/dev/sda2                          1,8G  125M  1,5G   8% /boot
tmpfs                               98M  4,0K   98M   1% /run/user/1000

$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0 61,9M  1 loop /snap/core20/1434
loop1                       7:1    0 55,5M  1 loop /snap/core18/2409
loop2                       7:2    0 61,9M  1 loop /snap/core20/1405
loop3                       7:3    0 79,9M  1 loop /snap/lxd/22923
loop4                       7:4    0 44,7M  1 loop /snap/snapd/15534
loop5                       7:5    0 55,5M  1 loop /snap/core18/2344
loop6                       7:6    0  1,1M  1 loop /snap/mosquitto/704
sda                         8:0    0   30G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0  1,8G  0 part /boot
└─sda3                      8:3    0 18,2G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0   10G  0 lvm  /
sr0                        11:0    1 1024M  0 rom  

# pvs
  PV         VG        Fmt  Attr PSize  PFree
  /dev/sda3  ubuntu-vg lvm2 a--  18,22g 8,22g
```

le disque sda fait 30 Go, la partition sda3 18 Go et la Filesystem root 10 Go
Le système utilise LVM

## Extension de la partition

Installer la commande growpart

```bash
# apt update
# apt install cloud-guest-utils

# growpart /dev/sda 3
CHANGED: partition=3 start=3719168 old: size=38221824 end=41940992 new: size=59195359 end=62914527

# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0 61,9M  1 loop /snap/core20/1434
loop1                       7:1    0 55,5M  1 loop /snap/core18/2409
loop2                       7:2    0 61,9M  1 loop /snap/core20/1405
loop3                       7:3    0 79,9M  1 loop /snap/lxd/22923
loop4                       7:4    0 44,7M  1 loop /snap/snapd/15534
loop5                       7:5    0 55,5M  1 loop /snap/core18/2344
loop6                       7:6    0  1,1M  1 loop /snap/mosquitto/704
sda                         8:0    0   30G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0  1,8G  0 part /boot
└─sda3                      8:3    0 28,2G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0   10G  0 lvm  /
sr0                        11:0    1 1024M  0 rom  

```

## Resize root logical volume to occupy all space

```bash
# pvs
  PV         VG        Fmt  Attr PSize  PFree 
  /dev/sda3  ubuntu-vg lvm2 a--  28,22g 18,22g

# sudo pvresize /dev/sda3
  Physical volume "/dev/sda3" changed
  1 physical volume(s) resized or updated / 0 physical volume(s) not resized

# pvs
  PV         VG        Fmt  Attr PSize  PFree 
  /dev/sda3  ubuntu-vg lvm2 a--  28,22g 18,22g

# vgs
  VG        #PV #LV #SN Attr   VSize  VFree 
  ubuntu-vg   1   1   0 wz--n- 28,22g 18,22g

# df -hT
Filesystem                        Type     Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv ext4     9,8G  8,4G  921M  91% /

# lvextend -r -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
  Size of logical volume ubuntu-vg/ubuntu-lv changed from 10,00 GiB (2560 extents) to 28,22 GiB (7225 extents).
  Logical volume ubuntu-vg/ubuntu-lv successfully resized.
resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/mapper/ubuntu--vg-ubuntu--lv is mounted on /; on-line resizing required
old_desc_blocks = 2, new_desc_blocks = 4
The filesystem on /dev/mapper/ubuntu--vg-ubuntu--lv is now 7398400 (4k) blocks long.


# df -hT
Filesystem                        Type     Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv ext4      28G  8,4G   19G  32% /

```