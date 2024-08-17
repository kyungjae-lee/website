[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Embedded Linux</a> > Introduction to uEnv.txt File

# Introduction to uEnv.txt File



## The Essence of uEnv.txt File

* uEnv.txt file is nothing but a collection of various environment variables which are initialized to a number of U-boot commands that automates the command execution.



## Important Background Knowledge

* Minicom window $\to$ Boot BBB (e.g., Boot from eMMC) to U-boot prompt

### U-boot Commands

* `help` will display all the U-boot commands supported by the current version of U-boot.

  Specify the command if you need to see only a specific command: `help <command>`

  ```plain
  => help
  ?       - alias for 'help'
  askenv  - get environment variables from stdin
  base    - print or set address offset
  bdinfo  - print Board Info structure
  boot    - boot default, i.e., run 'bootcmd'
  bootd   - boot default, i.e., run 'bootcmd'
  bootefi - Boots an EFI payload from memory
  bootelf - Boot from an ELF image in memory
  bootm   - boot application image from memory
  bootp   - boot image via network using BOOTP/TFTP protocol
  bootvx  - Boot vxWorks from an ELF image
  bootz   - boot Linux zImage image from memory
  cmp     - memory compare
  coninfo - print console devices and information
  cp      - memory copy
  crc32   - checksum calculation
  dfu     - Device Firmware Upgrade
  dhcp    - boot image via network using DHCP/TFTP protocol
  dm      - Driver model low level access
  echo    - echo args to console
  editenv - edit environment variable
  eeprom  - EEPROM sub-system
  env     - environment handling commands
  exit    - exit script
  ext2load- load binary file from a Ext2 filesystem
  ext2ls  - list files in a directory (default /)
  ext4load- load binary file from a Ext4 filesystem
  ext4ls  - list files in a directory (default /)
  ext4size- determine a file's size
  ext4write- create a file in the root directory
  false   - do nothing, unsuccessfully
  fastboot- use USB Fastboot protocol
  fatinfo - print information about filesystem
  fatload - load binary file from a dos filesystem
  fatls   - list files in a directory (default /)
  fatsize - determine a file's size
  fatwrite- write file into a dos filesystem
  fdt     - flattened device tree utility commands
  fstype  - Look up a filesystem type
  go      - start application at address 'addr'
  gpio    - query and control gpio pins
  gpt     - GUID Partition Table
  help    - print command description/usage
  i2c     - I2C sub-system
  iminfo  - print header information for application image
  imxtract- extract a part of a multi-image
  itest   - return true/false on integer compare
  load    - load binary file from a filesystem
  loadb   - load binary file over serial line (kermit mode)
  loads   - load S-Record file over serial line
  loadx   - load binary file over serial line (xmodem mode)
  loady   - load binary file over serial line (ymodem mode)
  loop    - infinite loop on address range
  ls      - list files in a directory (default /)
  md      - memory display
  mdio    - MDIO utility commands
  mii     - MII utility commands
  mm      - memory modify (auto-incrementing address)
  mmc     - MMC sub system
  mmcinfo - display MMC info
  mw      - memory write (fill)
  nfs     - boot image via network using NFS protocol
  nm      - memory modify (constant address)
  part    - disk partition related commands
  ping    - send ICMP ECHO_REQUEST to network host
  printenv- print environment variables
  pxe     - commands to get and boot from pxe files
  reset   - Perform RESET of the CPU
  run     - run commands in an environment variable
  save    - save file to a filesystem
  setenv  - set environment variables
  sf      - SPI flash sub-system
  showvar - print local hushshell variables
  size    - determine a file's size
  sleep   - delay execution for some time
  source  - run script from memory
  spl     - SPL configuration
  sspi    - SPI utility command
  sysboot - command to get and boot from syslinux files
  test    - minimal test like /bin/sh
  tftpboot- boot image via network using TFTP protocol
  true    - do nothing, successfully
  ums     - Use the UMS [USB Mass Storage]
  usb     - USB sub-system
  usbboot - boot from USB device
  version - print monitor, compiler and linker version
  ```

### U-boot Environment Variables

* Similar to Linux environment variables, U-boot also has a set of standard as well as user defined environmental variables which determine the behavior of the U-boot. This behavior can be altered by overriding or changing the environmental variable values.

* Use `printenv` to display all environment variables.

  ```plain
  => printenv soc
  soc=am33xx
  ```

* Use `setenv` to set the value of a specified environment. (Creates on if the environment variable is not present.)

  ```plain
  => setenv serverip 192.168.27.1
  => printenv serverip
  serverip=192.168.27.1
  ```

* You can also combine multiple U-boot commands delimited by `;` in one envinronment variable. (They all will run in one go!)

  ```plain
  => printenv bootcmd
  bootcmd=if test ${boot_fit} -eq 1; then run update_to_fit;fi;run findfdt; run init_console; run envboot; run disd
  ```

* Use `run` to run a command.

### Exercise

* Create a user-defined environment variable.

  ```plain
  => setenv my_mmc_list 'mmc list'
  => printenv my_mmc_list 
  my_mmc_list=mmc list
  => run my_mmc_list 
  OMAP SD/MMC: 0
  OMAP SD/MMC: 1
  ```

  > `mmc list` is a predefined U-boot command.
  >
  > MMC0 is the interface to $\micro$SD card.
  >
  > MMC1 is the interface to eMMC.







## References

Nayak, K. (2022). *Embedded Linux Step by Step Using Beaglebone Black* [Video file]. Retrieved from https://www.udemy.com/course/embedded-linux-step-by-step-using-beaglebone/