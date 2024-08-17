[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Linux Device Drivers</a> > Linux Kernel Module Build - Out-of-Tree Module

# Linux Kernel Module Build - Out-of-Tree Module



## Building an Out-of-Tree Module

* Command to build an external module:

  ```plain
  make -C <path_to_linux_kernel_source_tree> M=<path_to_your_module> [target]
  ```

  > 1. `make -C <path_to_linux_kernel_source_tree>` switch will trigger the **top-level Makefile** of the Linux kernel source tree.
  >
  >    It will enter `<path_to_linux_kernel_source_tree>` and run the top-level Makefile. At this time, kbuild rules (e.g., compiler switches, dependency list, version string) will be utilized to build the kernel modules.
  >
  > 2. `M=<path_to_your_module>` will direct the top-level Makefile to trigger the **local Makefile** in your working directory where the external modules to be compiled are stored.

  In Makefile syntax, it can be re-written as:

  ```makefile
  make -C $KDIR M=$PWD [Targets]
  ```

  > * `-C $KDIR` - The directory where the kernel source is located. `make` will change it to the specified directory when executing and will change it back when finished.
  >
  > * `M=$PWD` - Informs kbuild that an external module is being built. The value given to `M` is the absolute path to the directory where the external module (kbuild file) is located.
  >
  > * `[Targets]`
  >
  >   * `modules` - The default target for external modules. It has the same functionality as if no target was specified.
  >
  >   * `modules_install` - Install the external module(s). The default location is `/lib/modules/<kernel_release>/extra/`, but a prefix may be added with `INSTALL_MOD_PATH`.
  >
  >   * `clean` - Remove all generated files in the module directory only
  >
  >   * `help` - List the available targets for external modules

* Creating a local Makefile - In the local Makefile you should define a kbuild variable as below:

  ```makefile
  obj-<X> := <module_name>.o
  ```

  > `obj-<X>` is the kbuild variable and `X` takes one of the following values:
  >
  > * `X = n` - Do not compile the module
  > * `X = y` - Compile the module and link with kernel image
  > * `X = m` - Compile as dynamically loadable kernel module

  Example:

  ```makefile
  # Makefile
  obj-m := main.o
  ```

  > The kbuild system will build `main.o` from `main.c`, and after linking the kernel module `main.ko` will be produced.

### Exercise 1 (Host - Host)

* Check the prebuilt kernel version by running `uname -r` and build your own kernel module.

  In the `00_hello_world/` directory, run the following command to build the LKM against the host PC's Linux kernel version:

  ```plain
  make -C /lib/modules/5.19.0-41-generic/build/ M=$PWD modules
  ```

  Then, insert the LKM into the running kernel:

  ```plain
  sudo insmod main.ko
  ```

  Run `dmesg` to check if the LKM has successfully printed `Hello world!`.

  Remove the LKM from the running kernel:

  ```plain
  sudo rmmod main.ko
  ```

  Run `dmesg` again to check if the LKM has successfully printed `Good bye world!`.

* Note

  * At first, `sudo insmod main.ko` did not work on my PC. This turned out to be due to the "Secure Boot" option that was enabled on my PC. Disabling this option in the BIOS resolved this issue.
  * Although both `Hello world!` and `Good bye world!` showed up, the warning messages did not appear at insertion. Need to check why!

### Exercise 2 (Host - Target : Cross-Compile)

* In the file `/etc/sudoers` append `export PATH=$PATH:<path_to_toolchain_binaries>` to the `secure_path`. For example,

  ```plain
  Defaults        env_reset
  Defaults        mail_badpass
  Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:export PATH=$PATH:/home/klee/linux-device-drivers/workspace/downloads/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf/bin"
  Defaults        use_pty
  ```

* In the `00_hello_world/` directory where the LKM is located, run the following command to build the LKM against the target's Linux kernel version:

  ```plain
  sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -C /home/klee/repos/linux-device-drivers/workspace/source/linux_bbb_4.14/ M=$PWD modules
  ```

  > You are **cross-compiling** this time!

  You can run `file main.ko`, `modinfo main.ko`, or `arm-linux-gnueabihf-objdump -h main.ko` to check if the build has been successful.

* Transfer the built module to the target

  ```plain
  scp main.ko debian@192.168.7.2:/home/debian/drivers
  ```

  > You'll be asked to enter the PW of the target, and then the file will be transferred.

* Insert the LKM into the running kernel:

  ```plain
  sudo insmod main.ko
  ```

  > It will print the message right on the creen.

  Another way to see the message is to run `dmesg | tail`.

* Remove the LKM from the running kernel:

  ```
  sudo rmmod main.ko
  ```

### Update the Makefile

* Update the local Makefile to automate the out-of-tree module build process for both the host and the target:

  ```makefile
  obj-m := main.o
  ARCH=arm
  CROSS_COMPILE=arm-linux-gnueabihf-
  KERN_DIR=/home/klee/repos/linux-device-drivers/workspace/source/linux_bbb_4.14/
  HOST_KERN_DIR=/lib/modules/$(shell uname -r)/build/
  
  all:
      make ARCH=$(ARCH) CROSS_COMPILE=$(CROSS_COMPILE) -C $(KERN_DIR) M=$(PWD) modules
  
  clean:
      make ARCH=$(ARCH) CROSS_COMPILE=$(CROSS_COMPILE) -C $(KERN_DIR) M=$(PWD) clean
  
  help:
      make ARCH=$(ARCH) CROSS_COMPILE=$(CROSS_COMPILE) -C $(KERN_DIR) M=$(PWD) help
  
  host:
      make -C $(HOST_KERN_DIR) M=$(PWD) modules
  ```

