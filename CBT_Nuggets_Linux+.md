# Explain Linux Kernel and Boot Concepts

## 02. Difference between GRUB, and GRUB2
in `/boot/grub`
GRUB uses: `menu.lst`, `grub.conf`
GRUB2 uses just: `grub.cfg`

GRUB2: `grub.cfg`, hidden menu, can boot from iso


## 03. Boot file locations
`/etc/default`

Debian
`/boot/grub`
```
$ update-grub
```

RedHat
`/boot/grub2`
```
$ grub2-mkconfig -o /etc/grub2.cfg
```

DO NOT edit directly
`grub.cfg`


## 04. Boot Methods
PXE - Preboot Execution Environment
GRUB2 ISO booting is specific to Linux

## 05. Boot Modules and Files
- `initrd` - initialize RAM disk
- `initramfs` - (object stored inside kernel) - file system for krenel - not used after boot

## 06. Kernel Panic
Common causes of kernel panic:
- bad RAM
- Corrupt kernel update
- overclocked CPU

## 07. Loading Kernel Modules on Boot
- `/etc/modules` - for manual entries on what to load
- `/etc/modprobe.d/blacklist.conf` - for manual blacklisting
By blacklisting a kernel module, we esnusre the Linux system won't automatically load it
as a dependency of another module

## 08. Manipulating Kernel Modules
Modules live in `/lib/modules/`

Tools for inserting modules
- `insmod` - full path, no dependency check, fail without explanation
- `modprobe` - just module name, dependency check, needs **map**
modprobe is the frontend that uses insmod

Tools for managing modules
- `lsmod` - list inserted modules
- `rmmod` - remove inserted modules
- `depmod` - generates **map**



# Configure nad Verify Network Connections

## 09. Testing Network Connectivity
- `ping`
- `ip addr`
- `ifconfig` (outdated)
- `ip route`

## 10. Testnig DNS
- `dig`
- `nslookup`
- `host`

## 11. Locating Common Network Configuration Files
- `/etc/hosts`
- `/etc/resolv.conf`
- `/etc/nsswitch.conf`

## 12. Identifying Debian and ubuntu Network Configuration Files
- `/etc/network/interfaces` (older version)
- `/etc/netplan/*` (newer version)
  - `netplan apply`
- Network Manager (any version)
  - GUI
  - `nmtui` (console network manager)

## 13. Identifying Red Hat and CentOS Network Configuration Files
- Network Manager
- `/etc/sysconfig/newtork-scripts`

## 14. Network Bonding Modes
Switch supported (L3 switches or smart switches) or generic

Mode | Description   | Need switch support?
-----|---------------|---------------------
0    | balance-rr    | Sort of
1    | active backup | No
2    | balance-xor   | Yes
3    | broadcast     | Yes
4    | 802.3ad       | Yes
5    | balance-tlb   | No
6    | balance alb   | No

0. you need switch support *unless*
you connect computer directy to computer

one-by-one packet sending in cycles
1. if one port fails other takes over
2. packet is directed by MAC addresses (sender-reciever)
computed hash, mode 4 is better at that
3. for very specific cases, all port get the same traffic
4. Industry standard for directing packets 
**Highly recommended for switch that has support**
5. Balances incomming traffic
6. Balances incomming and outcomming traffic
**Highly recommended for switch without support**

## 15. Configuring Bonded Network Interfaces
- Ubuntu
  - `/etc/netplan/*` (add bond config)
  - `/proc/net/bonding/*` (check bond status)
- CentOS
  - `/etc/sysconfig/network-scripts` (add bond config)
  - TODO: revisit this (!)  



# Manage Storage in Linux Environment

## 16. Understanding GPT and MBR
- MBR - Master Boot Record
  * Old system
  * Supports disks up to 2 TB and can have 4 partitions (without using extended partitions)
- Protective MBR - The blank device that prevents older BIOS systems from assuming a GPT drive
- GPT - GUID Partition table 
  * New system 
  * Supports much more space and partitions
  * Stores multiple copies around the disk for redundancy, has CRC for error checking, making it more robust


## 17. Filesystem Hierarchy
- `/` everything starts on **root directory**
- virtual filesystem
  - `/proc`, `/sys` belongs here
  - dynamic files/pseudofiles that are managed by kernel
- remote filesystem
  - NFS, SMB, or other
  - mounted on actual filesystem

- `tree` program to show tree structure of directory
- `ls -a`

used for relative paths
- `..` parent directory
- `.` current directory
-  `~` shortcut for **home** directory

Just remember this:
*Linux has just one big monolithic filesystem.*

## 18. Creating Partitions
- `parted` partition editor
- `gparted` graphical partition editor
- `lsblk`
- `cat /proc/partitions`
- `fdisk` (for older systems)

## 19. Formatting a Partition with Various Filesystems
File systems
- `ext`
 * most common
 * mature
 * later versions support journaling
- `xfs`
 * older
 * still used by CentOS (and has its own set of tools)
- `btrfs`
 * new, but not widely used
- dos
  - `ntfs`
  - `vfat`
  - `fat32`

Making filesystems
- `mkfs.*` (hit tab twice to list specific tool)

## 20. Mounting Partitions Manually and at Boot
You can mount only on **empty folder**.

Ways of mounting
- manually
  * `mount`
  * `umount` for unmountig
- on boot
  * edit `/etc/fstab`
    - file structure:
      * `file system` UUID or device (eg. `/dev/sda1`)
      * `mount point` directory to mount
      * `type` filesystem (eg. `ext4`)
      * `options` use `defaults` for default config
      * `dump` deprecated, use 0
      * `pass` integrity check, root `/` directory should be 1, others (if needed) 2, for no check use 0

## 21. Checking and Scanning Linux Filesystems
Steps to determine whether scan or not on boot
- check `/etc/fstab` if the pass is set to 1 or 2
  * scan if true
- check if the current mount count is higher than the allowed maximum count (if maximum count is -1 DO NOT scan)
  * scan if true
- boot system and mount partitions

Tool `tune2fs` allows to get/set the filesystem parameters (including maximum allowed count)

root `/` directory cannot be scanned manually after the boot.
For manual scanning boot the (other) system from USB/CD and use `fsck` on root `/` directory

## 22. Understanding LVM
Physical Volumes (**PV**) are groupped in Volume Group (**VG**) which acts like a big 'chunk' 
from which you 'carve out' Logical Volumes (**LV**) formatted with File Systems (**FS**).

- `pvdisplay`
- `lvdisplay`

## 23. Creating an LVM System
- `pvcreate`
- `vgcreate`
- `lvcreate`

A physical volume can *NOT* be larger than the volume group it's a part of.

## 24. RAID Levels
- RAID 0 (stripe)
- RAID 1 (mirror)
- RAID 5
- RAID 6

## 25. Configuring RAID Array with mdadm
- `mdadm` tool for creating RAID
 * Example: `mdadm --create --verbose /dev/md0 --level=5 --raid-devices=4 /dev/sdb1 /dev/sdc1 /dev/sde1 /dev/sde1`
- `cat /proc/mdstat` for RAID info
- `mdadm --detail --scan` RAID config
- `mdadm --detail --scan > /etc/mdadm/mdadm.conf` Save RAID config (to work on boot)



# Compare and Contrast Virtualization Concepts

## 26. Identifying Virtual Machines and Containers
- virtual machines require hypervisors that run virtualized hardware that run full operating systems inside them
- containers run on operating systems themselves have their own isolated file system and run isolated processes

Oversimplification
- VMs - isolated OSes
- containers - isolated processes

## 27. Virtual Machine Templates
- OVF - Open Virtualization Format - files that describe VM
  * HD images
  * RAM requirements
  * Network requirements
  * and so on...
- OVA - Open Virtualization Appliance - OVF files archived with tar
  * basically a one .tar file renamed to .ova

## 28. Understanding Virtual Machine Networking
- Networks - virtualized, but still acts like real
  * bridged 
    - shares connection with the host
    - on the same network with the host
 * NAT
    - just like connection in your home
    - host acts like a router 
 * local/host
    - not connected at all to external network 
    - isolated network
    - *ALTHOUGH* one of the VMs can have two (different) network connections, it's called
      * **dual-homed** - has two NICs
        - one is connected to local/host network
        - second is connected to external network (acts as bridge)

## 29. Understanding Virtualization Storage Options
- provisioning - reserving space on host storage
 - thin - grows as needed
 - thick - all at once

- persistent volume - exists even if we destroy VM
- blobs - storage through API, not a direct storage

## 30. Identifying Linux Virtualization Tools
- KVM - Kernel Virtualization Module
- libvirt - indirect layer between KMV and management tool
- virsh/virt-manager - management tools
  * `virsh` is CLI
  * `virt-manager` is GUI

## 31. Bootstraping Linux Instalation
"From CD-rom to fully installed and configured system"

Installation configuration files that "do" all the interaction during installation.
- CentOS - **Kickstart** file to **Anaconda** installer
- Debian - **Preseed**, but also kickstart file

Cloud-init 
- bare-bones intstall from cloned image 
- then on initial boot runs customization scripts



# Configure Localization Options

## 32. Configuring Clocks and Timezones
- same thing
 * Hardware clock
 * Real Time Clock (RTC)
 * CMOS clock

- same time, not the same thing
  * UTC - Coordinated Universal Time - Standard, not a time zone
  * GMT - Greenwich Mean Time - Time zone that has the same time as UTC

  Usually hardware clock is set as UTC, then system tmie is set accordingly to time zone.
  - `date`
  - `timedatectl` - shows the details about local time, universal time, rtc time, and time zone (also NTP and others)
  - `ls -l /etc/localtime` - shows symbolic link to timezone
  - `ln -s /usr/share/zoneinfo/America/New_York` - set time zone (`rm /etc/localtime` first)

## 33. Configuring Language and Localization Options
- ASCII - American Standard for Computer Information Interchange
  * 7 bits
  * 128 characters
  * English language
- Unicode
  * characters for almost all languages
  * rules of rendering
  * rules of encoding
- UTF - Unicode Transformation Format
  * A way to encode Unicode for computers

  - `locale` - environnmet variable containing locale detais
  - `localectl` - manage locale
    * after changes in locale, you have to log out and back in to reestablish environment variables