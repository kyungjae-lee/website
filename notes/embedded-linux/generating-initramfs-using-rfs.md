[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Embedded Linux</a> > Generating "initramfs" Using RFS

# Generating "initramfs" Using RFS



## What is initramfs?

* initramfs = **Init**ial + **RAM** based + **f**ile **s**ystem
* It is a compressed file system with all those directory hierarchy which was covered in the previous section, made to reside in RAM. "Compressed" because RAM is limited in size and we need to secure RAM space as much as possible for other important purposes.
* Linux mounts this file system (i.e., initramfs) as the initial file system (RAM acts as a storage for initramfs.)
* Using initramfs is optional.
* initramfs gets loaded at an early stage of the boot process and gets embedded into the kernel. It gives all the minimal requirements to boot the Linux kernel successfully on the board just from RAM without worrying about other peripherals. What you should store in initramfs is left to your product requirements. You may store all the important drivers and firmware, you may keep your product specific scripts, early graphic display logos, etc.



## Why is initramfs Necessary?

* Let's understand this with an example.

  Say you have a product and your product has USB interfaces, mass storage devices like SD card, networking peripherals such as Ethernet, and a display. Now, to operate this wide range of peripherals, all the device drivers must be in place. That means all the drivers must be loaded into the kernel space. And along with the drivers, some peripherals may require the firmware binaries to operate.

  One idea is to make sure that all those drivers are "built-in" into the kernel. This is not a good idea because that makes your Linux kernel very specific to your product and it will drastically increase the Linux kernel image size.

  Another good way is that you prepare a minimal file system which contains all the necessary drivers and firmware, and load it on to RAM, and ask the Linux to mount that file system during the boot process. (You can use the "kernel boot arguments" to tell the kernel that your file system resides in RAM.) When the kernel mounts the file system from RAM, it loads all the required drivers for your product and since the drivers are in place, all the peripherals of your product are ready to operate properly.

  After that, you can remove this RAM based file system and use (switch to) some other advanced file system which may reside on you other memory devices such as eMMC or SD card, etc. You can even mount a file system from the network.



## How to Store initramfs in RAM

There are two ways:

1. Make initramfs **built-in** to the Linux kernel during the compilation, so when the Linux starts booting, it will place the initramfs in the RAM and mounts it as the initial root file system and continues.
2. Load the initramfs from some other sources into RAM of your board and tell the Linux kernel about it (i.e., at which RAM address the initramfs is present) using the "kernel boot arguments".



## How to Generate the initramfs

1. Download and extract the sample root file system which is taken from TI software SDK.

   $\to$ `am335x_tiny_filesystem` is used here.

2. `cd` into the extracted directory and run the following 2 commands on terminal:

   ```plain
   find . | cpio -H newc -o > ../initramfs.cpio
   cat ../initramfs.cpio | gzip > ../initramfs.gz
   ```

   > L1: Generating a **cpio** archive
   >
   > L2: Generating a **gz** archive

   Check if you see `initramfs.gz` at the end of these steps.

3. Install `mkImage` command.

   ```plain
   sudo apt install u-boot-tools
   ```

   > This will install all the U-boot related tools along with the `mkImage` utility.

4. Make the initramfs U-boot friendly by attaching the U-boot header with the load address and other info.

   ```plain
   mkimage -A arm -O Linux -T ramdisk -C none -a 0x80800000 -n "Root Filesystem" -d ../initramfs.gz ../initramfs
   ```

   ```plain
   Image Name:   Root Filesystem
   Created:      Sun Mar 19 12:48:25 2023
   Image Type:   ARM Linux RAMDisk Image (uncompressed)
   Data Size:    12280431 Bytes = 11992.61 KiB = 11.71 MiB
   Load Address: 80800000
   Entry Point:  80800000
   ```

   Great! Now you have the **initramfs** file which includes the **U-boot header** which can be used as RAM based file system when required.





## References

Nayak, K. (2022). *Embedded Linux Step by Step Using Beaglebone Black* [Video file]. Retrieved from https://www.udemy.com/course/embedded-linux-step-by-step-using-beaglebone/