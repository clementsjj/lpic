# System Architecture

## Pseudo File Systems
`/proc/` and `/sys`
### What is it? 
- A regular file system is a method of laying out files and folders on a physical hard disk
  - Info that is contained within headers that point to files on system
  - Everthing in linux is a file itself
- A pseudo file system does not exist on a physical hard drive. Created by kernel after computer boots and only exists in ram.
- When system boots, kernel creates another pseudo file system using the same directories
- Multiple pseudo file systems in linux.

### `/proc`
- Every process is related to a PID, which is represented as a folder here
- /proc/cpuinfo --> shows info on the cpu, does not exist until the system boots and pseudo file system is created
- /proc/1 --> Everything that happens in init
- Find some info about your computer system and the processes that are currently running


### `/sys`
- the more pertinent information that belongs within sys is information about the hardware that is currently connected to the computer
and a Kernel itself. 
- No process information 

## Kernel Modules
- Kernel
  - Core framework of the OS
  - GNU --> Pertains to tools
  - Linux --> Pertains to Linux Kernel
  - Provides a way for the rest of the system to operate with hardware, mem, netowrking, and itself (subsystems)
  - Monolithic Kernel
    - Kernel handles all memory management and hardware device interactions
    - Extra functionality can be loaded and unloaded dynamically through kernel modules
    - Ensures that the system will not need to be rebooted into a different kernel image for added functionality
      - New driver ... don't need to reboot the whole system
- `uname` for additional information on kernel
  - -m
    - machine type
  - -rm
    - release version and machine type
  - -a
    - more information
- kernel modules
  - `lsmod`
    - List kernel modules
  - `modinfo <module_name>`
    - More info on module
  - `modprobe -r <name>`
    - remove kernel module
    - `modprobe <name>` adds the module

## Investigating Hardware
- udev
  - actual linux **device manager** for the kernel; links information on system hardware to /dev
  - When you attach a hdd, udev detects this and passes info via D-bus service that passes all info to other services.
  The hdd is attached to the /dev pseudo file system, which contains actual handles.
  - Any piece of hardware pickedup by udev gets sent to /dev
- /dev
  - Contains information on all of the connected hardware on a system
  - Something like /dev/dri contains info about graphics card, but the files are passing information, so it appears as binary
  - attach a usb drive, and it creates an entry in /dev
- D-Bus
  - Sends data messages between applications
  - A conduit of information about what is going on in the system
  - udev utilizes dbus to notify users and the system when new hardware is attached
- Some commands
  - `lspci`
    - list out all of the pci devices attached to computer
    - -k
    - -v
      - verbose?
  - `lsusb`
    - Displays information on attached usb devices
    - -t --> tree view
  - `lscpu`
    - displays info on processors on a system
  - `lsblk`
    - displays info on all block devices on a system, in tree view
 
 ---
 
 # Boot the System
 
 ## Linux Boot Sequence
 - After a computer starts, the BIOS on the motherboard checks on all  hardware and input and output devices. 
 - Once everything checks out, boot can begin.
 - The boot program, grub (brand unified boot loader) will look at section of harddrive that contains the data needed to boot an OS. 
 - Boot loader then loads the kernel
 - Kernel loads inititial ram disk
  - contains a bunch of device drivers
 - After kernel is g2g, starts the initialization system.
 - Initialization system continues the process
 
 - Boot logs
  - Logs generated when the system boots up.
  - Typically volotile. Generated from ring buffer, area of ram where all system messages are written to
  - Ring buffer typically used to see if hardware is getting recognized by the Kernel, but it may not be recognized by udev or show up in /dev.
  
  - dmesg
    - traditional utility used for viewing the kernel ring buffer
    - systemd is now more commonly used instead of the old init system, and uses journalctl
  - journalctl -k
    - systemd utility to view the kernel ring buffer within the systemd journal
    - logs every event in the computer
  
 
