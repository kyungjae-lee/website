[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Linux Device Drivers</a> > Linux Kernel Module Build - Introduction

# Linux Kernel Module Build - Introduction



## Introduction to Kernel Module Build

* A kernel module can be built in 2 ways:

  * Statically linked to the kernel image

  * Dynamically loadble $\leftarrow$ our focus!

    * **In-tree module** (Internal to the Linux kernel tree)

      These modules are the ones approved by the kernel developers and maintainers that are already part of the Linux kernel source tree.

    * **Out-of-tree module** (External to the Linux kernel tree)

      A module written by a general user, which is not approved by the kernel authorities and may be buggy, to be built and linked to the running kernel, is called an out-of-tree module.

      This method taints the kernel. Kernel issues a warning saying that out-of-tree module has been loaded. You can safely ignore the warning!

* Building a kernel module (out-of-tree)

  * Modules are built using "**kbuild**" which is the build system used by the Linux kernel.

  * Modules must use "kbuild" to stay compatible with changes in the build infrastructure and to pick up the right flags to GCC. Also, the "kbuild" will automatically choose the right flags for you.

  * To build external modules, you MUST have a prebuilt kernel source available that contains the configuration and header files used in the build. This is because the modules are linked to the object files found in the kernel source tree.

    You cannot compile your module on one Linux kernel version and load it into the system running on a different kernel version. The module load may not be successful, and even if it is, you'll still encounter run-time issues with the symbols.

    [!] Rule of thumb: Have a prebuilt Linux kernel source tree on your machine and build your module on it.

    Two ways to obtain a prebuilt kernel version:

    * Download kernel from your distributor and build it by yourself
    * Install the Linux-headers- of the target Linux kernel

  * This ensures that as the developer changes the kernel configuration, his custom driver is automatically rebuilt with the correct kernel configuration.

  * Reference: https://www.kernel.org/doc/Documentation/kbuild/modules.txt
