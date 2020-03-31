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
 
 BIOS -> Boot Sector (w/ the Boot Loader) -> Kernel -> Initial RAM Disk -> Device Initialization
 
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
  
 ## Init
 - based off the sysvinit used in UNIX systems
 - Services are started one after the other -- meant to keep things simple, but can hang the system
 - How it works
  - After kenel and brings in the initial ram disk, seeks out an initialization system to hand over control of the computer
  - First place the computer looks for is in `/sbin/init`
  - First thing init does is read settings in `/etc/inittab`
    - determines what run level the system should be operating in
      - predefined configuration; can only operate in one run level at a time; applies ot system as a whole
    - Run Levels:
      - 0 - Halt
      - 1 - Single User Mode
        - Only root
      - 2 - Multi-user mode (no networking)
      - 3 - Multi-user mode (with networking)
        - historicall this is default
      - 4 - unused  but can be used to setup custom
      - 5 - Multi-user, with networking and a graphical desktop
      - 6 - reboot
    - `/etc/inittab`
      - <identifier>:<runlevel>:<action>:<process>
- `/etc/rc.d/`
  - Red Hat Based
  - rc = run commands
  - rc.sysinit 
    - does some housecleaning before entering run level
  - rc.local
    - ran after run level has completely loaded
    - usually customized by sys admin
  - `rc3.d`
    - the K = to be killed followed by number order
    - the S = to be started followed by number order
- `/etc/init.d`
  - Debian Based
  - Contains the scripts for the services on the system
  - `/etc/init.d/rc`
    - Script that orchestrates how the runlevel scripts run and what occurs when a runlevel changes

## Upstart
- The init daemon replacement created by Ubuntu in 2006 (v 6.10, written by scott remnant)
- Can start in async manner, decrease boot times
- Uses real time events
- has ability to monitor services, makes attempts to start again if failure
- `/sbin/init` --> startup(7) --> mountall(8) and `/etc/init/rc-sysinit.conf` --> telinit(8) --> runlevel(7) --> `/etc/init/rc.conf` --> Login
  - Multiple other jobs running at the same time; this is simplified view
- init is static and does not natively respond to changes on a system, while upstart is dynamic and can respond to changes on the system
- A changed on a linux system is called an event; events trigger upstart jobs; jobs have two catagories: TASKS and SERVICES
  - Waiting -> Starting -> Running (-^ respawning) -> Stopping -> Killed -> Post-stop -> Waiting

## Systemd
- Predominant initialization service used on Linux. 
- removed need to have shell scripts
- bash has to go through bash interpreter making new processes etc.; slows everything down
- To get around, shell scripts replaced with C code.
- Still compat with older system v init scripts
- Unit Files
  - default - `/usr/lib/systemd/system` (shouldn't edit)
  - `/etc/systemd/system` (if you need to edit)
  - `/run/systemd/system` -- Runtime Unit Files
  - `systemctl list-unit-files` -- View all unit files on a system
  - Components:
    - Follow ini style from ms-dos
    - [Unit]
    - Discription=Multi-User System
    - Documentation=man:systemd.special(7)
    - Requires=basic.target
    - Conflicts=rescure.service rexcure.target
    - After=basic.target rescure.service rescue.target
    - man 5 systemd.unit
    - `systemctl cat something.unit` -- print out the contents of the unit file specified
 - How does systemd boot the system?
  - Kernel looks for `/sbin/init/`
  - systemd took the place of `/sbin/init`/ via symbolic link... actually starting `/lib/systemd/systemd`
  
---

## Change Runlevels/Boot Targets && Shutdown/Reboot
### runlevels


- `runlevel` 
  - Shows current runlevel and prev
  - `cat /etc/inittab`
- `telinit <init>` or `init <num>`
  - must be root
  - `telinit 3`
- Can change runlevels before finishes booting
  - `reboot`
  - press any key to interrupt boot process which brings us to grub
  - press "a" keyto modify kernel args
  - Looking at tail of command
  - press <space> and type 3, <enter>

##### **Systemd Target Unit**
- systemd Target
  - Target is aunit that syncs up other units when the computer boots or changes states
  - Most common use of a target is to get the system into a new state
  - Other units that associate themselves with a target env
  - Types:
    - multi-user.target
      - like a runlevel 3
    - graphical.target 
      - like a runlevel 5
    - rescue.target
      - isolated for repairs, like level 1
    - basic.target
      - basic system, used during boot process before another target takes over
    - sysint.target
      -system initialization 
- `systemctl cat garphical.target`
- `systemctl list-unit-files -t taget`
  - all target files on system with current state
- `systemctl list-units -t taget`
  - all units currently have in active state
- `systemctl get-default`
- `systemctl set-default multi-user.target`
- `systemctl isolate multi-user.target`
- `systemctl rescue`
- `systemctl poweroff`

### Reboot and Shutdown
- Reboot commands
  - `telinit 6`
  - `reboot`
  - `shutdown -r now`
  - `systemctl isolate reboot.target`
    - only if using systemd
- Wall command
  - broadcast a message to all logged in users
  - `wall <enter> message <ctrl+d>`
- Shutdown Commands
  - `poweroff`
  - `telinit 0`
  - `shutdown -h +1`
  - `systemctl isolat poweroff.target`
  - `systemctl poweroff`
  - `shutdown -P`
- acpid
  - Advanced configuration and power interface
  - registers system events (such as pressing the power button or closing laptop lid)
  - `/etc/acpi`
    - events and actions
      - `cat /etc/acpi/events/`
      - `cat /etc/acpi/actions/`
 

  
