[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Embedded Linux</a> > Yocto Project Workflow

# Yocto Project Workflow



## Yocto Project Workflow

Assume the `yocto-project/` as the root directory.

1. Download the Poky source code

   ```plain
   git clone git://git.yoctoproject.org/poky
   ```

   > I cloned this repo into `yocto-project/source`.

2. Checkout the latest branch/release (dunfell)

   ```plain
   git checkout dunfell
   ```

   > Yocto version and the host OS version are inter-dependent. "zeus" works for Ubuntu-18.04, "dunfell" works for Ubuntu-22.04.

3. Prepare the build environment (Poky provides a script `oe-init-build-env`, which should be used to setup the build environment. The script will set up the environment to use Yocto build system, including adding the BitBake utility to your PATH.)

   ```plain
   source poky/oe-init-build-env <build_directory>
   ```

   > Here, `<build_directory>` is an optional argument for the name of the directory where the environment is set in case it is not given, it defaults to `build`.
   >
   > I used `../build` for `<build_directory>` so that running this command would create `yocto-project/build/`.

   This script will move you into the `<build_directory>` and create two files (`local.conf`, `bblayers.conf`) inside the `conf/` directory.

   Also, now the BitBake utility will be available. (Check with `bitbake -h`)

4. Build Linux distribution

   ```plain
   bitbake <image_name>
   ```

   ```plain
   time bitbake core-image-minimal
   ```

   > `core-image-minimal` is a small image allowing a device to boot, and it is very useful for kernel and bootloader tests and development.





## Reference

Linux Trainer. (2020). *Embedded Linux using Yocto* [Video file]. Retrieved from https://www.udemy.com/course/embedded-linux-using-yocto/