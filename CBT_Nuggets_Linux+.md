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

## 18. Creating Partitions

## 19. Formatting a Partition with Various Filesystems

## 20. Mounting Partitions Manually and at Boot

## 21. Checking and Scanning Linux Filesystems

## 22. Understanding LVM

## 23. Creating an LVM System

## 24. RAID Levels

## 25. Configuring RAID Array with mdadm



# Compare and Contrast Virtualization Concepts