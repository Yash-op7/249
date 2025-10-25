### Overview of Directory Categories:
1. System Binaries: for core commands
2. Boot & Kernel-related: files that help the system start
3. Config files: define how linux behaves
4. User related directories: personal folders, root access permissions
5. Shared Libraries: that support essential programs
6. Mount Points and Media used for external devices
7. System and runtime info that reflect whats happening in real time
8. Multi-user resources; for software and logs
9. Temporary files used briefly by the system
10. Optionally Software installed seperately
### The Root Dir `/`
linux has a single unified file system, where / is the starting point of the file system, dont confuse it with /root which is the personal home folder of the root user who has full administrative access.

### /bin
- contains essential command line tools, general purpose commands used by all users  that are needed for the system to function properly especially during boot, eg: ls, cp, mv, cat
- always available and should not be messed with by regular users
### /sbin
- system binaries
- holds specialized system utilities, special commands critical for managing and maintaining the system, especially for tasks like mounting file systems, checking disks or shutting down the system, ex: mount, fsck, shutdown, 
- these tools required admin privileges to run
- no need to touch this dir
### /lib
- many commands in /bin and /sbin rely on shared libraries similar to dll files on windows, 
- /lib32, /lib64 store 32 and 64 bit libraries
- also holds kernel modules which are small pieces of code that can be loaded in the linux kernel on demand to extend its functionality
- no need to touch this either
### /usr
- Unix System Resources
- contains most of the system's user-space applications, libraries and docs, a typical user doesnt usually modify this dir directly, but its where many installed programs live, 
- it mirrors several dir from the root level like `/usr/bin`, `/usr/sbin`, `/usr/lib`, this structure is because /usr was meant to hold non essential components which complements the essential tools in /bin and /lib, for organization, as only the root level stuff is required for boot
- read only and managed by the system, dont touch
### /boot
- all the files the sys needs to boot up
- ex: the linux kernel, initial ram disk, `initrd`, `initramfs`, `GRUB`, `grub.cfg` (tells the system how to load the OS)
- dont touch
### /dev
- Device files that act as interfaces to your hardware
- In linux everything including devices are treated like files ([[File descriptors & Buffers]])
- ex: `/dev/sda` might represent your first hard drive, `/dev/null` - for virtual devices, all devices, keyboards, mouse show up here
- two main types of devices: block devices like hard drives which handle data in chunks and character devices like keyboards which handle data as a stream of characters.
- interesting to look at but modifying this folder can damage the hardware, dont touch
### /etc - editable text config
- central location for system wide configuration files
- everything from network settings to user accounts to startup scripts, and service configuration
- ex: `/etc/passwd` - holds basic user information, `/etc/fstab` - defines how drives are mounted at boot, most of these are txt file designed to readable and editable by sys admins, 
- change carefully
### /home
- personal space for regular users
- each user has their own subdir here, eg: `/home/yash`, where they store documents, downloads, and personal settings, 
- these folders store user data separate from system files, making backups and account management easier
- user specific config files also live here and are hidden (hidden files start with . in linux, eg /bashrc) by default
- theres also a shared or public folder used to exchange files between users, by default users dont have access to each others home folders, unless permissions are changed, 
- meant to be customized by users
- the root user has a separate home dir located at /root for security
### /media
- where modern linux systems automatically mount removable media, like usb drives, CDs, DVDs.
- each device is accessible here via a subfolder
- user friendly and doesnt require sudo
- /dev holds the device files that represent the hardware itself, whereas media is where the devices are mounted and made usable
### /mnt
- reserved for manual temporary device mounts used typically for troubleshooting
- not used often
### /proc
- is a virtual fs
- doesnt represent real files on disk, but provides a dynamic, in memory view of the system
- acts as a live interface to the linux kernel and running processes
- here folders are named after process IDs, eg: `/proc/1234` and system info files like `/proc/cpuinfo`, and `/proc/meminfo`
- for monitoring the system's working in real time
### /sys
- another virtual fs, 
- acts as a dynamic interface between the linux kernel and the hardware
- the kernel exposes info about the devices, drivers, system buses and ports, you'll find folders representing CPUs, memory blocks, USB ports and even the system's power state
- used for configuring hardware config and not used often
### /run
- holds temporary runtime data, created on boot, 