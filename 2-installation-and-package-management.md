# Linux Installation and Package Management
---
## Design Hard Disk Layout
- Main File System Locations
  - Primary locations to know:
    - `/` | Root
    - `/var` | Logfiles and other dynamic content; variable content
    - `/home` | User's home directory where personal files are stored; every user gets their own
    - `/boot` 
    - `/opt`| short for optional and used for third-party vendors
  - Swap space
    - temp storage that acts like ram
    - when a percentage of ram is full, kernel moves less used data to swap
    - swap partition usually
    - sometimes a swap file (like page file on windows)
      - much slower than partition
    - Sizing
      - Older rule of thumb: 1.5x - 2x size of ram
      - nowadays generally don't want smaller than 50% of your ram
  - Partitions
    - `/dev/sda` -> dev directory location that contains hardware attached to system
    - sda is first connected device indicated by 'a'
  - Mount point
    - an entire partition mounted to a directory
    - like `/dev/sda2` -> `/home` ; `/dev/sda3` -> `/boot` ; `/dev/sda1` -> `/`
    - taking a partition or disk and mounting it to a directory
  - Commands
    - `mount`
      - list every partition and mount point used on the system
      - shows a lot of pseudo file systems
      - nvme -- newer systems, newest type of solid state harddrives
    - `lsblk`
      - list out block devices, basically a harddisk
    - `fdisk -l /dev/diskname`
      - need root `su -`
    - `swapon --summary`
      - Shows a summar of the swap usage on a system ,same information can be found in `/proc/swaps`
    
- Intro to LVM
  - Logical Volume Manager
  - Allows the creation of 'groups' of disks or partitions that can be assembled into a single (or multiple) filesystem(s):
    - Can be used for nearly any mount point except for /boot
      - GRUB cannot read lvm metadata
    - Flexibility -- allows for resizing of volumes
    - Snapshots - allows for 'point in time' copies of you logical volume
  - PV (Physical Volume) --> VG (Volume Group) --> LV (Logical Volumes) --> file system
  - Commands
    - pvs
      - physical volume scan
      - lists out the physical volumes in an lvm group
    - vgs
      - lists out the volume groups within an lvm group
    - lvs
      - lists out the logical volumes within an lvm group


---
## Install a Boot Manager
- Legacy Grub
  - BIOS checks everything out, then locates bootloades with `boot.img` in master boot record (mbr -- first 512 bytes of hdd) (stage 1).
  - boot.img is looking for core.img (stage 1.5) and load it. 
  - core.img purpose is to loacate `/boot/grub` (stage 2)
    - grub.conf (redhat) or menu.lst (debian) file
    - and device.map which shows were kernel and os to be booted
  - Install
    - grub-install [device] (grub-install '(hd0)'
      - findmnt /boot
        - shows actual location of where the boot partition is found
    - grub
      - find /grub/stage1 (diff than `find`)
        - (hd0,0)
    - executing `grub-install` on a system that is actively up and running is potentially dangerous
      - this step is teypically done from a live cd/usb where grub is getting installe to a new disk
  - Steps:
    - `grub` opens a new "grub shell"
      - `help`
      - `find /grub/stage1` 
      - `quit`
    
- GRUB2
  - Differnce between MBR (Master boot record) and GPT (GUID Partition Table)
    - MBR (Legacy Grub)
      - Traditionally supported only 26 total partitions (4 partitions, with one partition extended to 23 partitions)
      - Each partition limited to 2TB
    - GPT (GRUB2)
      - Supports up to 128 Partitions
      - Partition size up in the ZB (zettabyte) range (1ZB = 909,494,701.8 TB)
      - Needs UEFI
        - "Unified Extensible Firmware Interface"
        - Replacement for traditional BIOS, can act in legacy BIOS mode
        - Requires 64bit os
        - Prevents unauthorized OS from booting on the system
  - GRUB2 on GPT with UEFI
    - UEFI BIOS --> Stage 1 MBR boot.img --> GPT Header (GPT style disk) --> Partition Entry Array --> Stage 1.5 core.img --> 
    - core.img is looking for `/boot/efi` partition (has to be vfat or FAT32 file system) ("ESP")
    - stage 2 `boot/grub2` --> `grubenv` and `themes`
  - Commands
    - Red Hat Based --> grub2-<command>
    - Debian Based --> grub-<command>
    - `grub2-editenv list`
      - view the default boot entry for the grub config file
      - can modify `/etc/default/grub`
      - 
    - `grub2-mkconfig`
      - creates (or updates) a /boot/grub1/grub.cfg file based on entries from the /etc/default/grub file
    - `update-grub`
      - command that can be used to update a GRUB2 configuration after changes to /etc/default/grub have been made, found on debian based systems
     

- Interacting with the Boot Loader
  - GRUB Legacy
    - In boot menu
      - [a]
        - append options to the kernel boot line
      - [c]
        - open the GRUB command line
          - `setup (hd0)` (just rewrites the config)
      - [esc]
        - escapes out of any GRUB menu
  - GRUB2
    - [e] key
      - Used to edit a GRUB menu item
    - Ctrl+x or F10
      - Boots a selection or modified line
    -

---
## Manage Shared Libraries


---
## Use Debian Package Management


---
## Use RPM and YUM Package Management


---
## Linux as a Virtual Guest


---
