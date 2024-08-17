[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Linux Device Drivers</a> > Project Setup

# Project Setup



## Project Environment Setup

* Install necessary packages on the host:

  ```plain
  sudo apt update
  ```

  ```plain
  sudo apt install build-essential lzop u-boot-tools net-tools bison flex libssl-dev libncurses5-dev libncursesw5-dev unzip chrpath xz-utils minicom
  ```

* Setup the workspace:

  * `custom_drivers/`
  * `downloads/`
  * `patches/`
  * `source/`

* Download boot images and root filesystem

  * Boot images - `pre-built-images.zip`
    * `am335x-boneblack.dtb` - Device tree binary of BBB
    * `MLO` - Primary boot loader (Memory LOader)
    * `u-boot` - U-boot bootloader image
    * `uEnv.txt` - U-boot commands and environment settings
    * `uImage` - Kernel image 
  * Debian root filesystem - `bone-debian-9.9-iot-armhf-2019-08-03-4gb.img.xz` (https://beagleboard.org/)

* Prepare $\micro$SD card

  * Partition 1 - BOOT / FAT16 / Stores boot images (e.g., MLO, U-boot, kernel image) / 512MB
  * Partition 2 - ROOTFS / EXT4 / Stores Debian root filesystem / Rest of the $\micro$SD card

* Download cross-compiler and toolchain

  * To cross-compile the Linux kernel, Linux application, and kernel modules to ARM Cortex-Ax architecture, cross-compiler is necessary.
  * The SoC AM335x from TI is based on ARM Cortex-A8 processor of ARMv7 architecture.
  * Download the cross-compiler - `gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf` (https://releases.linaro.org/components/toolchain/binaries/7.5-2019.12/arm-linux-gnueabihf/)
    * Older versions: https://releases.linaro.org/components/toolchain/binaries/
    * Newer versions: https://snapshots.linaro.org/gnu-toolchain/

* Add the toochain binary path to the PATH variable (`.bashrc` in home directory)

  1. Go do the home directory

  2. Open `.bashrc` file using an editor

  3. Add the following command to the `.bashrc` file

     ```plain
     export PATH=$PATH:<path_to_toolchain_binaries>
     ```

     > e.g., `export PATH=$PATH:/home/klee/linux-device-drivers/workspace/downloads/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf/bin`

     Or simply do

     ```plain
     echo "export PATH=$PATH:<path_to_toolchain_binaries>" > ~/.bashrc
     ```

  4. In the terminal, type `arm` and hit `tab` to see if the system recognizes the binaries.



## Connecting the Host with the Target (USB-to-Serial TTL Cable)

* Pin mapping

  | USB-to-Serial TTL Cable Pins | BBB J1 Header Pin Outs |
  | ---------------------------- | ---------------------- |
  | Pin 1 - GND (Black)          | Pin1 - GND             |
  | Pin 4 - RXD (Oragne)         | Pin 5 - TXD            |
  | Pin 5 - TXD (Yellow)         | Pin 4 - RXD            |

  > Make sure to cross-connect TXDs and RXDs!



## Enabling Internet Over USB

* Internet over USB
  * BBB board can communicate with the Internet over the USB cable by sharing the host PC's internet connection.
  * A separate Ethernet cable is not necessary for the BBB board's internet connection.
  * The required drivers are enabled by default in the kernel and loaded when Linux boots on the BBB board.
  * But, you need to enable the internet sharing feature on your host PC to use this service.

### Target Settings

1. First run `ifconfig` and see if your system recognizes `usb0` interface.

   If `usb0` interface does not show up, reboot the board and check again.

   If it still does not show up, execute the following commands and check again:

   ```plain
   sudo modprobe g_ether
   sudo ifconfig usb0 192.168.7.2 up
   ifconfig
   ```

   At this point you'll be able to `ping 192.168.7.1` (to host), but not `ping www.google.com` (to the Internet).

2. Add name server address in `/etc/resolv.conf`:

   ```plain
   nameserver 8.8.8.8
   nameserver 8.8.4.4
   ```

3. Add name server address in `/etc/network/interfaces`

   ```plain
   iface usb0 inet static
   	address 192.168.7.2
   	netmask 255.255.255.252
   	network 192.168.7.0
   	gateway 192.168.7.1
   	dns-nameservers 8.8.8.8		<-- add this
   	dns-nameservers 8.8.4.4		<-- add this
   ```

4. Add default gateway address by running the following command:

   ```plain
   sudo route add default gw 192.168.7.1
   ```

   > We are using the host PC as the default gateway.

   Whenever rebooting the board, you need to run this command to get Internet connection. For simple SSH connection to the host PC, running this command is not required.

### Host Settings

1. Run the following commands:

   ```plain
   sudo iptables --table nat --append POSTROUTING --out-interface <network_interface_name> -j MASQUERADE
   ```

   > `<network_interface_name>` - Your primary connection to the network could be wireless or wired. You must use the name as listed by the command `ifconfig`. (In my case `wlp61s0`)

   ```plain
   sudo iptables --append FORWARD --in-interface <network_interface_name> -j ACCEPT
   ```

   ```plain
   sudo -s
   echo 1 > /proc/sys/net/ipv4/ip_forward
   ```

   > Simply running `sudo echo 1 > /proc/sys/net/ipv4/ip_forward` won't work!

   Whenever rebooting the board, you need to run these commands. So, may be a good idea to create a short script and execute it on every reboot. For example:

   ```plain
   #!/bin/bash
   ##To run this script do
   ##1. chmod +x usbnet.sh 
   ##2. ./usbnet.sh 
   iptables --table nat --append POSTROUTING --out-interface <network_interface_name> -j MASQUERADE
   iptables --append FORWARD --in-interface <network_interface_name> -j ACCEPT
   echo 1 > /proc/sys/net/ipv4/ip_forward
   ```

   > Make sure th replace `<network_interface_name>` with a real name.
