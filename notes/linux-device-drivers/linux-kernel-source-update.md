[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Linux Device Drivers</a> > Linux Kernel Source Update

# Linux Kernel Source Update



## Linux Kernel Source Update

1. Clone the kernel source from BBB official GitHub repository (https://github.com/beagleboard/linux) $\to$ This will clone the master branch of the repository. You will need to checkout a specific branch (i.e., kernel source version).

   * 4.14 is used for this project

     In the `workspace/source/` directory:

     ```plain
     git clone https://github.com/beagleboard/linux.git linux_bbb_4.14
     ```

   * `cd` to `linux_bbb_4.14/` and run `git checkout 4.14`.

2. Compile and generate the kernel image from the downloaded kernel image directory (`workspace/source/linux_bbb_4.14/`)

   * Step 1:

     ```plain
     make ARCH=arm distclean
     ```

     > Removes all the temporary folder, object files, images generated during the previous build. 
     >
     > Also, deletes the `.config` file if created previously.

   * Step 2:

     ```plain
     make ARCH=arm bb.org_defconfig
     ```

     > Creates a `.config` file by using default config file given by the vendor (Default config file can be found in `workspace/source/linux_bbb_4.14/arch/arm/configs/`)

   * Step 3 (Optional):

     ```plain
     make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig
     ```

     > Run this command only if you want to change some kernel settings before compilation.

   * Step 4: Compile kernel source

     ```plain
     make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- uImage dtbs LOADADDR=0x80008000 -j4
     ```

     > Creates a kernel image `uImage`.
     >
     > Compiles all device tree source files, and generates `dtbs`.

     `#error New address family defined, please update secclass_map.` can be resolved by:

     1. `scripts/selinux/genheaders/genheaders.c` $\to$ comment out `#include <sys/socket/h>`
     2. `scrpts/selinux/mdp/mdp.c` $\to$ comment out `#include <sys/socket.h>`
     3. `security/selinux/include/classmap.h` $\to$ add `#include <linux/socket.h>`

     `fatal error: mpc.h: No such file or directory` error may arise, which is caused by the lack of multiple precision complex floating-point library development package `libmpc-dev`. Resolve this error by running:

     ```plain
     sudo apt install libmpc-dev 
     ```

   * Step 5: Build kernel modules

     ```plain
     make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- modules -j4
     ```

     > Builds and generates in-tree loadable(M) kernel module (`.ko`).

   * Step 6:

     ```plain
     sudo make ARCH=arm modules_install
     ```

     > Installs all the generated `.ko` files in the default path of the computer (`/lib/modules/<kernel_ver>`).

     Now, you should be able to see `/lib/modules/4.14.108+/` directory.

3. Update the $\micro$SD with the new kernel image, dtb and kernel modules

   * To update the kernel image and dtb in the $\micro$SD:

     * Copy `workspace/source/linnux_bbb_5.10/arch/arm/boot/uImage` to `/mdeia/klee/BOOT/` ($\micro$SD card).

     * Copy `workspace/source/linnux_bbb_5.10/arch/arm/boot/dts/am335x-boneblack.dtb` to `/mdeia/klee/BOOT/` ($\micro$SD card). 

       > Don't forget update dtb! It will hang the boot process at 'Starting kernel ...'.
       >
       > Select the dtb file whose name matches the one that is present in the `BOOT` partition of the $\micro$SD card.

   * To update the kernel modules in the $\micro$SD:

     * Copy newly installed kernel modules `/lib/modules/4.14.108+/` to `/media/klee/ROOTFS/lib/modules/` ($\micro$SD card).

     Run `sync` to flush left-over contents in the buffer to the media

4. Boot the board from the updated $\micro$SD card. 

5. Check the kernel version after login:

   ```plain
   uname -r
   ```

   It should display the updated kernel version. (`4.14.108+` in my case)
