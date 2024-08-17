[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Embedded Linux</a> > Root File System (RFS) & Directory Structure

# Root File System (RFS) & Directory Structure



## Root File System (RFS)

* The **root file system (RFS)** is the file system Linux mounts to the `/` (root).

* A file system is a collection of files organized in a standardized directory structure.

* The standard for the Linux file system is called "**File system Hierarchy Standard (FHS)**". See the following links for more details:

  [https://wiki.Linuxfoundation.org/lsb/fhs-30](https://wiki.linuxfoundation.org/lsb/fhs-30)

  [https://en.wikipedia.org/wiki/File system_Hierarchy_Standard#cite_note-2](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard#cite_note-2)



## Directory Structure

* In a typical file system you will find the below folder structure, even though all these folders are not required for Linux to boot and mount the file system successfully.

  See also, [http://refspecs.Linuxfoundation.org/FHS_3.0/fhs/ch03s02.html](http://refspecs.linuxfoundation.org/FHS_3.0/fhs/ch03s02.html).

### Directory Structure Overview

* `bin/` - Essential command binaries
* `boot/` - Static files of the boot loader
* `dev/` - Device files
* `etc/` - Host-specific system configuration
* `lib/` - Essential shared libraries and kernel modules
* `media/` - Mount point for removable media
* `mnt/` - Mount point for mounting a file system temporarily
* `opt/` - Add-on application software packages
* `run/` - Data relevant to running processes
* `sbin/` - Essential system binaries
* `srv/` - Data for services provided by this system
* `tmp/` - Temporary files
* `usr/` - Secondary hierarchy
* `var/` - Variable data

### Directory Structure in Detail

* `bin/`

  This directory contains **binaries** of Linux commands which are used by both the system admins and users.   

  You don’t need the root privilege from your system admin to execute these  commands. Remember that this folder will  not contain binaries for all the Linux commands. There is a restriction on what types of commands have to be placed in this directory, because  these binaries can be executed by the common user.   

  Below are some the commands you will find it in the `bin/` directory.

  * `cat` - Utility to concatenate files to standard output
  * `chgrp` - Utility to change file group ownership
  * `chmod` - Utility to change file access permissions   

  * `chown` - Utility to change file owner and group   

  * `cp` - Utility to copy files and directories   

  * `date` - Utility to print or set the system data and time   

  * `dd` - Utility to convert and copy a file   

  * `df` - Utility to report file system disk space usage   

  * `dmesg` - Utility to print or control the kernel message buffer   

  * `echo` - Utility to display a line of text   

  * `false` - Utility to do nothing, unsuccessfully   

  * `hostname` - Utility to show or set the system's host name   

  * `kill` - Utility to send signals to processes   

  * `ln` - Utility to make links between files   

  * `login` - Utility to begin a session on the system   

  * `ls` - Utility to list directory contents   

  * `mkdir` - Utility to make directories   

  * `mknod` - Utility to make block or character special files   

  * `more` - Utility to page through text   

  * `mount` - Utility to mount a file system   

  * `mv` - Utility to move/rename files   

  * `ps` - Utility to report process status   

  * `pwd` - Utility to print name of current working directory   
  
  [!] Note: You can see that commands related to "repairing", "recovering", "restoring", "network configuration", "modules install/remove" are NOT found in this directory.
  
* `boot/`

  This directory contains the **boot related files**, which are required to boot the Linux. 

  This directory may be read by the boot loader to read the boot images like Linux kernel image, dtb, vmLinux, initramfs, etc.

  So, this directory may be accessed by the boot loader even before the kernel boots and mounts the file system.

* `dev/`

  This is the place where you can find the **device files**. 

  In UNIX/Linux, everything is treated as a file. Networking devices, memory devices, serial devices, parallel devices, I/O devices such as keyboard, mouse and display, everything is treated as a file.

  So, this directory has the file entry for every device. For example, 

  * I2C devices - `/dev/i2c-0`, `/dev/i2c-1`
  * RAM - `/dev/ram0` 
  * 2 partitions of SD card - `/dev/mmcblk0p1`, `/dev/mmcblk0p2`
  * Serial devices - `/dev/ttyS0`, `/dev/ttyO0`

  The user space application can use these device files to access the corresponding devices.

  It is the responsibility of the respective drivers to populate this directory with the device files.

* `etc/`

  This directory contains:

  * Run level scripts, which will be used during the different run levels.
  * Startup and shutdown scripts.
  * Various scripts related to services like start/stop networking, start/stop NFS, etc.
  * Various configuration files like passwd, hostinfo, etc.
  * Various network configuration files

* `lib/`

  This directory contains:

  * The dynamically loadable kernel modules. (Later, you'll see, when you compile the kernel modules and when we run `modules install` command, all the kernel modules will go and sit in this directory under the sub directory "modules".)
  * Essential shared libraries (`*.so`) for dynamic linking. e.g., C standard library (`libc`), math library, python library, etc.

* `media/`

  This directory is the mount point for the removable media like USB flash drive, SD cards, camera, cell phone memory, etc. For example, when an SD card is connected to the PC, there will be 2 device files created for each partition; `/dev/sdb1` and `/dev/sdb2`. And, these 2 device files are automatically mounted under the `/media/` directory, and I can access these 2 partitions just like any other directories.

  * CD-ROM - `/media/cdrom`
  * USB - `/media/<your USB flash drive name>`

* `mnt/`

  This directory is where temporary file systems get mounted. 

  The system admins can use Linux commands to temporarily mount and un-mount the file system, if they want to transfer any file.

* `opt/`

  "opt" stands for **optional**. This directory will be used when you install any software packages for your Linux distribution.

  e.g., If `apt get install <package>` is run, then the package will be installed in this directory.

* `sbin/`

  This directory contains the commands which come in the category of system administration which are used by the system admins for the purpose of networking configurations, repairing, restoring and recovering.

  Also contains some commands that require the root privilege to run.

  Commands that are stored in `sbin/`:

  * `fastboot` - Reboot the system without checking the disks (optional)
  * `fasthalt` - Stop the system without checking the disks (optional)
  * `fdisk` - Partition table manipulator (optional)
  * `fsck` - File system check and repair utility (optional)
  * `fsck.*` - File system check and repair utility for a specific file system (optional)
  * `getty` - The getty program (optional)       
  * `halt` - Command to stop the system (optional)       
  * `ifconfig` - Configure a network interface (optional)       
  * `init` - Initial process (optional)
  * `mkfs` - Command to build a file system (optional)
  * `mkfs.*` - Command to build a specific file system (optional)
  * `mkswap` - Command to set up a swap area (optional)
  * `reboot` - Command to reboot the system (optional)
  * `route` - IP routing table utility (optional)
  * `swapon` - Enable paging and swapping (optional)
  * `swapoff` - Disable paging and swapping (optional)
  * `update` - Daemon to periodically flush file system buffers (optional)
  
* `home/`

  This directory contains the **home** directory of each user.

  Each use only has write access to their own home directory and must obtain elevated permissions (root privilege) to modify other files on the system.

  This directory is used to store personal data of the user.

  In a single user environment you may have directory like `/home/klee`. In a multiple user environment, `/home/klee`, `/home/hkim`, `/home/ylee`, etc.

* `srv/`

  "srv" stands for **service**. This directory contains **data for services provided by the system**.

  If you are using Apache HTTP server to serve a website, you'd likely store your website's files in a directory inside the `srv/` directory.

* `tmp/`

  This directory contains the temporary files of the applications.

* `usr/`

  According to FHS, this directory is a **secondary hierarchy**. 

  The `usr/` directory may contain the following sub-directories:

  * `bin/` - Commands for the user programs

    e.g., If you have installed firefox on your machine, it must be located under `/usr/bin` not under `/bin`, because it is a binary of a user-installed program. Similarly `zip` command will also be found under `/usr/bin`.

  * `include/` - Header files included by C programs

  * `lib` - Contains shared libraries, linker/loader files, which enable your `/usb/bin` and `usr/sbin` commands to execute.

  * `local` - Local hierarchy (empty after main installation)

  * `sbin` - Contains privileged commands which may be used by the system admins for the system administration purposes

  * `share` - Architecture-independent data



## See Also

* For more information, see [http://refspecs.linuxfoundation.org/FHS_3.0/fhs/ch03.html](http://refspecs.linuxfoundation.org/FHS_3.0/fhs/ch03.html).





## References

Nayak, K. (2022). *Embedded Linux Step by Step Using Beaglebone Black* [Video file]. Retrieved from https://www.udemy.com/course/embedded-linux-step-by-step-using-beaglebone/