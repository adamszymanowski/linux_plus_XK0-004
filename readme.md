# Comptia Linux+ (XK0-004) Exam Study Notes
- [Notes from CBT Nuggets Course](https://github.com/adamszymanowski/linux_plus_XK0-004/blob/master/CBT_Nuggets_Linux%2B.md)

## Skills

Skill | Modules | Flashcards
------|---------|-----------
Explain Linux Kernel and Boot Concepts      | 001-008 | *Done*
Configure and Verify Network Connections    | 009-015 | *Done*
Manage Storage in Linux Environment         | 016-025 | TODO
Compare and Contrast Virtualization Methods | 026-031 | TODO  

# References and Further Reading
- (UALSAH) - Unix and Linux System Administration Handbook - fifth edition
- ArchLinux Wikipedia
  * [Boot process](https://wiki.archlinux.org/index.php/Arch_boot_process)
- IBM Docs
  * [Network bonding modes](https://www.ibm.com/docs/en/linux-on-systems?topic=recommendations-bonding-modes)
- [Network bonding modes in CentOS](https://www.thegeekdiary.com/what-are-the-network-bonding-modes-in-centos-rhel/)

# Exam Objectives
- [CompTIA Exam Objectives](https://www.comptia.jp/pdf/comptia-linux-xk0-004-exam-objectives.pdf)
- [CompTIA Linux+ Acronyms](https://github.com/adamszymanowski/linux_plus_XK0-004/blob/master/Acronyms.md)

## 1.0 Hardware and System Configuration
### 1.1 Explain Linux boot process concepts
* Boot Loaders
  - GRUB
  - GRUB2
* Boot options
  - UEFI/EFI
  - PXE
  - NFS
  - Boot from ISO
  - Boot from HTTP/FTP
* File locations
  - /etc/default/grub
  - /etc/grub2.cfg
  - /boot
  - /boot/grub
  - /boot/grub2
  - /boot/efi
* Boot modules and files
  - Commands
    - mkinitrd
    - dracut
    - grub2-install
    - grub2-mkconfig
  - intramfs
  - efi files
  - vmlinuz
  - vmlinux
* Kernel panic



# Reminder for silly me
```
git remote add origin git@github:adamszymanowski/linux_plus_XK0-004.git
git remote add origin https://github.com/adamszymanowski/linux_plus_XK0-004.git
git push -u origin master
```
