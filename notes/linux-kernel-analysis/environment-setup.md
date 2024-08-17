[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Linux Kernel Analysis</a> > Environment Setup

# Environment Setup



## Repository Setup

1. Create a GitHub repository to which you want to import the Linux kernel source. (e.g., [https://github.com/kyungjae-lee/linux-kernel-v6.4.2.git](https://github.com/kyungjae-lee/linux-kernel-v6.4.2.git))

2. Clone the Linux kernel source repository to your local machine.

   ```plain
   git clone --depth=1 --branch v6.4.2 <source_repository_url> <local_directory>
   ```

   > The last argument specifies the name of the local directory into which the Linux kernel source will be cloned.

3. Move into the local directory

   ```plain
   cd <local_directory>
   ```

4. Remove the `.git` directory.

   ```plain
   rm -rf .git/
   ```

5. Restructure your local directory according to your needs. (I've moved the entire source tree into a newly created subdirectory named `linux`.)

6. Convert the local directory to a Git repository.

   ```plain
   git init
   ```

7. Create a new remote called `origin` located at your GitHub repository.

   ```plain
   git remote add origin <your_repository_url>
   ```

   > Once you do this, in your push commands, you can push to `origin` instead of typing out the whole URL.

8. Add the change in the working directory to the staging area.

   ```plain
   git add .
   ```

9. Perform the initial commit.

   ```plain
   git commit -m "Initial commit"
   ```

10. Push your local changes to your online repository.

    ```plain
    git push -u origin main
    ```




## Build Tool Installation

* Install the tools necessary for kernel compilation/build.

  ```plain
  sudo apt install -y gcc-aarch64-linux-gnu g++-aarch64-linux-gnu \
  	cscope libssl-dev libncurses-dev\
  	autoconf automake autotools-dev curl libmpc-dev libmpfr-dev \
  	libgmp-dev gawk build-essential bison flex texinfo \
  	gperf libtool patchutils bc zlib1g-dev libexpat-dev
  ```

* Please see [https://www.kernel.org/doc/html/latest/process/changes.html](https://www.kernel.org/doc/html/latest/process/changes.html) for more details.



## Kernel Build (Cross-Compilation)

### Using a Shell Script

1. Create a shell script in the root path of the local directory.

   ```shell
   #!/bin/bash
   
   # Shell script is to configure the build environment and initiate the process of building
   # the Linux kernel for the ARM64 architecture using the "aarch64-linux-gnu-" cross-compiler
   
   # Get the path of the directory containing the script (current working directory)
   ROOT_DIR="$( cd "$(dirname "$0")" ; pwd -P )"
   
   # Create the "out" directory to store build output
   OUT_DIR="$ROOT_DIR/out"
   echo "$OUT_DIR"
   
   # Define variables for the kernel name and the build log file path
   BUILD_LOG="$ROOT_DIR/build_log.txt"
   
   # Move into the "linux" directory where the kernel source is located
   cd linux
   
   # Clean up any leftover files from previous kernel builds in your source directory
   make mrproper
   
   # Create a default configuration for building the Linux kernel targeting the "ARM64" architecture
   # using the "aarch64-linux-gnu-" cross-compiler
   make O=$OUT_DIR ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- defconfig
   
   # Do additional configurations (Optional; Uncomment the follwoing command if necessary)
   # This command opens an interactive menu that allows you to customize the configuration settings
   # for building the Linux kernel targeting the "ARM64" architecture using the specified 
   # cross-compiler (`aarch64-linux-gnu-`).
   #make O=$OUTPUT ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- menuconfig
   
   # Start the kernel build process for the "ARM64" architecture using the specified cross-compiler,
   # utilizing multiple cores (parallel compilation) to speed up the build process.
   # 2>&1 | tee $BUILD_LOG: Redirect both standard output and standard error to the $BUILD_LOG file,
   # while also printing them to the terminal.
   make O=$OUT_DIR ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j$(nproc) 2>&1 | tee $BUILD_LOG
   ```

2. Give the shell script execute permission.

   ```plain
   sudo chmod +x build_kernel_aarch64.sh
   ```

3. Run the shell script.

   ```plain
   sudo ./build_kernel_aarch64.sh
   ```

   When the build is complete, `vmlinux` will be created in `out/`, and `Image` will be generated in `out/arch/arm64/boot/`.

