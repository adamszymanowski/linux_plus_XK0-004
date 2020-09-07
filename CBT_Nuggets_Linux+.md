# Explain Linux Kernel and Boot Concepts

## 01. BIOS and UEFI
- BIOS
  - Basic Input Output System
  - older
  - uses MBR (Master Boot Record)

- UEFI
  - Unified Extensible Firmware Interface
  - newer
  - has a specialized partition for booting
    - supports way more partitions
    - supports Secure Boot

  UEFI is a replacement for BIOS.

## 02. Difference between GRUB, and GRUB2
Grand Unified Bootloader

- GRUB
  - legacy 
  - in `/boot/grub` directory there are
    - `menu.lst`
    - `grub.conf`
  - difficult to modify
  - boot menu usually displays on boot

- GRUB2
 - newer
 - in `/boot/grub` directory there is just
    - `grub.cfg`
- can boot from ISO, USB, UUID, device
- customizable in `/etc/default/grub`
- hidden boot menu (press shift!)



## 03. Boot file locations
* Debian
after changes in `/etc/default/grub` file run `update-grub` to update the actual boot file `/boot/grub`

* RedHat
after changes in `/etc/default/grub` file run `grub2-mkconfig -o /etc/grub2.cfg` to update the actual boot file `/etc/grub2.cfg`

No matter what distribution,

look around `vi /etc/grub2.cfg`, `ls -l /etc/grub*`, and so on... to see what's going on.

DO NOT edit directly
`grub.cfg` or `grub2.cfg`


## 04. Boot Methods
- Hardware
  * PXE - Preboot Execution Environment
    - image stored on the newtork TFTP server
  * iPXE - enchanced PXE
    - more options, for example image can be downloaded over HTTP
  * USB
  * CD, HDD

- Software
  * ISO booting via GRUB2


## 05. Boot Modules and Files
```
                                                      module
                                                        |
BIOS/UEFI ---> GRUB/GRUB2 ---> vmlinux/vmlinuz ---> full kernel - module
                            |              |            |
                          initrd          initranFS   module
                                          [dracut]    
```
Generic stripped down version of Linux kernel is stored in:
- `vmlinux` - kernel file
- `vmlinuz` - same kernel file, but compressed

- `initrd` - initialize RAM disk 
  * just enough driver and module information, to be able to have the Linux kernel access file system,
  so that it can get to its modules
  * temporary staging, not used after boot

- `initramfs` - object stored inside kernel - file system in RAM for kernel

## 06. Kernel Panic
Common causes of kernel panic:
- bad RAM
  * remove one stick at a time to eliminate the bad one
- Corrupt kernel update
  * use previous kernel version (use GRUB) and fix  stuff
- overclocked CPU

## 07. Loading Kernel Modules on Boot
Linux kernel is almost always good at loading modules automatically, but sometimes you need to load them manually,
or tell which ones not to load.

- `/etc/modules` - for manual entries on what to load
  * also loads dependencies automatically
  * .. but if wrong version is loaded automatically, we can blacklist it

- `/etc/modprobe.d/blacklist.conf` - for manual blacklisting

By blacklisting a kernel module, we ensure the Linux system won't automatically load it
as a dependency of another module

## 08. Manipulating Kernel Modules
Modules live in `/lib/modules/` (for each kernel version) `ls /lib/modules` 

Tools for inserting modules
- `insmod` 
  * full path
  * no dependency check
  * fail without explanation
- `modprobe`
  * just module name
  * dependency check
  * needs **map**

`modprobe` is the frontend that uses `insmod`

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

NOTE: (add flash cards to the topis above)

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
 * local/private/host-only
    - not connected at all to external network 
    - isolated network
    - *ALTHOUGH* one of the VMs can have two (different) network connections, it's called
      * **dual-homed** - has two NICs
        - one is connected to local/host network
        - second is connected to external network (acts as bridge)

## 29. Understanding Virtualization Storage Options
- provisioning - reserving space on host's storage
 - thin - but using as much as needed, and growing to the needs
 - thick - but all at once upfront

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

  - `locale` - get locale-specific information
  - `localectl` - Control the system locale and keyboard layout settings
    * after changes in locale, you have to log out and back in to reestablish environment variables

  
NOTE: (Add flash cards to the topisc below)
# Install, Update, and Configure Software

## 34. Installing Tarballs
Three step process
1. Extract
  - `tar -zxvf stuff.tar.gz`
  - `cd ./stuff`
2. Compile
  - `make` (usually)
3. Install, it depends, either:
  - `make install`
  - run script, E.g. `./install.sh`
  - move to appropreate directory E.g. `/usr/local/bin`

## 35. Managaing .deb Packages
- `apt`
  * newer
  * simple
  * **USE THIS**
- `aptitude`
  * older
  * still works
  * **DON'T USE**
- `apt-get`
  * *oldest*
  * still works
  * **DON'T USE**

- `dpkg`
  * actual installer, but doesn't resolve dependencies

- `apt update` updates packages repositories, usyally you must run this before installation

## 36. Managing .rpm Packages
- YUM - Yellowdog Updater Modifier
  * handles dependencies
  * updates repo when installing/uprgading, *no need* for doing it manually, like in `apt`
- DNF - DaNdiFied YUM (really!)
  * in Fedora, will replace YUM
- RPM - RPM Package manager
  * low level tool, but doesn't handle dependencies

## 37. Configuring APT Repositories
Add repositories to this file:
- `/etc/apt/sources.list`
Then you need to update `apt`
- `apt update`
but you need to add the GPG key too, for security reasons or else won't work (there will be errors), E.g.
- `wget -qO- https://deb.opera.com/archive.key | apt-key add -`
- `apt-key list` to list GPG keys
Update with added key wil succeed
- `apt update`

Adding repositories throug PPA, E.g.
- `add-apt-repository ppa:webupd8team/atom`
* it adds repository, GPG key and update the sources all at once

## 38. Configuring YUM Repositiories
- `/etc/yum.conf`
- `/etc/yum.repos.d/` folder with individual files in `<file>.repo`
- add repo
- edit config

## 39. Identifying Non-RPM and Non-APT Package Managers
- Arch Linux - `pacman`
- OpenSUSE - `zypper`



# Managing Users and Groups

## 40. Managing Local Users
- `adduser`
  * takes care of all the details that you would have to explicitly pass to `useradd`

Lower level tools for managing users
- `useradd`
- `usermod`
- `userdel`

## 41. Managing Local Groups
- primary - same as username (this group is used when E.g. creating a file)
- secondary/supplementary

- `groups <user>` - lists all the groups user belongs to

Lower level tools for managing groups
- `groupadd`
- `groupmod`
- `groupdel`

## 42. Querying User Accounts
- `whoami` - on what account you're currently logged in
- `who` - shows who is logged in
- `w`- shows who is logged in and what they are doing
- `pinky` - print user information
- `id` - print user and its group ids
- `last` -  listing of last logged users

## 43. Managing Group and Password File
- `/etc/passwd` - user info is stored here (E.g. home directories), but not passwords, every one has access to it, *encrypted passwords are no longer stored in here* (it used to be this way), everyone has acces to this file
- `/etc/shadow` - user encrypted passwords are stored in here **only root** user can access it

See permissions with `ls -l /etc/passwd/` and `ls -l /etc/shadow/`

Same thing with groups, althoug groups rarely have password
- `/etc/group`
- `/etc/gshadow`

Edit password or group files with these commands
- `sudo vipw`
- `sudo vipgr`
And shadow files with these
- `sudo vipw -s`
- `sudo vipgr -s`

## 44. Configuring User and Group Quotas
In `/etc/fstab` disk must have `usrquota` parameter added.
If enabled, run these command to check.
- `sudo quotacheck -au`
- `ls /mnt/disk/`

Setting quota:
- `quotaon -a`
-  `edquota <username>`
You can set it either on :
- **blocks** (size) or 
- **inodes** (number of files)
limits:
* soft limit - warning
* hard limit - can't write new files

Writing empty files:
`dd if=/dev/zero of=/file1 bs=1k count=400` - this will write 400kb empty file

## 45. Identyfing User Profiles
Exact locations depend on the distribution, options in **bold** are most likely.
- All Users
  * one or ohter - profile setting for shell
    * `/etc/bash.bashrc`
    * `/etc/bashrc`
  - and also
    - `/etc/profile` - login shell
    - `/etc/environment/` - environment variables on login

- Individual
  * one or other
    * `/home/user/.profile`
    * `/home/user/.bash_profile`
  - and also
    - `/home/user/.bashrc`



# Create, Modify, and Redirect Files

## 46. Chosing a Text Editor
- `nano`
- `vi`

## 47. Using More, Less, Head, Tail
- `more`
- `less`
- `head`
- `tail`

## 48. Searching for Lines of Text with Grep
These two are equivalent
- `grep -F word ./file`
- `cat ./file | grep -F word`

## 49. STDIN, STDOUT, adn STDERR
- STDIN
  * `<`
  * `|`
- STDOUT
  * `>`
- STDERR
  * `2>`

## 50. Understainding Output Redirection Tricks and Tools
- `/dev/null`
  * bit bucket, black hole
  * `echo "Hello" > /dev/null`
  * `ls jnkewr > /dev/null 2>&1`
- `tee`
  * writes to flie and STDOUT
  * `cat file.txt | tee copy.txt`
- `xargs`
  * build and execute from STDIN
  * `cat file.txt | xargs mkdir` - will create folders based on file.txt list

## 51. Understanding Text Manipulation with Command-Line Tools
- `sort` - sorts output
- `wc` - word count
- `cut` - cut sections from lines
- `paste` - merge lines of files

## 52. Text Processing with Awk and Sed
- `sed` - steram editor
- `awk` - Aho, Weinberger, Kerighan

## 53. Using Hard Links and Soft (Symbolic) Links
Files are referenced in file allocation table to actual file location on hard drive.
- soft link 
  * reference in allocation table to allocation table itself where it references actual file location on hard drive.
  * i.e link to another **inode**
  * i.e. point to another filename on the file system (?)
  * they break when the file is moved

- hard link 
  * new reference in allocation table but pointing to file location (duplicated reference)
  * i.e. link to the same **inode**
  * they don't break, but they take space

A demo:
  ```
  pushd ~
  echo "Hey, a file!" > file.txt
  ln -s file.txt soft_linked.txt
  ls -lai *.txt
  cat soft_linked.txt
  mv file.txt /Documents
  ls -lai *.txt
  cat soft_linked.txt
  ln /Documents/file.txt hard_linked.txt
  ls -lai *.txt
  cat hard_linked.txt
  mv /Documents/file.txt .
  ls -lai *.txt
  cat hard_linked.txt
  cat soft_linked.txt
  ```

## 54. Find and Locate
- `find`
  * searches 'live', not fastest, but can find anything
  * E.g. `find / -name *MyFile* 2> /dev/null`
  * since it searches everywhere it can, there might be *Permission denied* errors, hence redirecting STERR to `/dev/null` to superss error messages

- `locate`
  * uses cache so it's super fast
  * cache is updated once a day, but cna be updated by `updatedb`

## 55. Copying Files Over the Network
- `ssh` - secure shell - remote connection
- `scp` - secure copy
  * uses `ssh` to copy
  * `scp <source> <target>`
  * either *source* and *target* can be specified as `user@hostmane:/path`
- `rsync` - more advanced remote copying



# Manage Local Services

## 56. Managing System Services with Systemctl
- `systemctl`
  * `systemctl status <service>`
  * on boot:
    * `systemctl enable <service>`
    * `systemctl disable <service>`
  * now
    * `systemctl start <service>`
    * `systemctl stop <service>`

## 57. Managing Runlevels with SysV
This is generally not used now (but still sometimes).
- `runlevel`
- `telinit`
- `/etc/inittab`

level | Debian/Ubuntu    | CentOS/SUSE
------|------------------|------------
0     | halt             | halt
1     | single user mode | single user mode
2     | full multi user  | multi user, no net
3     | nothing          | multi user, with net
4     | nothing          | *not used*
5     | nothing          | Multi user GUI
6     | reboot           | reboot

## 58. Managing Targets with SystemD
Compairson between SysV and SystemD

runlevel | Boot target
---------|------------
0        | poweroff
1        | rescue
3        | multi-user
5        | graphical
6        | reboot

- `systemctl get-default`
- `systemctl set-default multi-user.target`
- `systemctl isolate multi-user`

## 59. Managing Services with SysV
- `/etc/init.d/`
- `service`
  * E.g `service sshd start`
- `chkconfig`
  * E.g.:
    - `chkconfig sshd`
    - `chkconfig --list sshd`
    - `chkconfig --level 3 on`

## 60. Managing Services with SystemD
- `systemctl`
  * on boot settings
    - `enable`
    - `disable`
  * current settings
    - `start`
    - `stop`
    - `status`