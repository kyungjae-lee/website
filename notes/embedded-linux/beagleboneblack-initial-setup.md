[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Embedded Linux</a> > BeagleBoneBlack(BBB) Initial Setup

# BeagleBoneBlack(BBB) Initial Setup



## BBB Web Interface

* The BBB Debian OS running on the BBB board already runs a web server. You can connect to it using its IP address (192.168.7.2). 

  Here, you wil be directed to online IDE where you can test-run various demos to see if your initial board setting is correct.

* BBB board supports Ethernet over USB cable so you need not connect Ethernet cable to your board. Just connect your board with your host machine via USB cable.

* First install network driver. On ubuntu, go to `/media/klee/BEAGLEBONE/Drivers/Linux/FTDI/` and run `mkudevrule.sh` file.
* To check the connection of the board to the host machine, open a terminal and ping 192.168.7.2.



## SYSFS

* SYSFS, is a type of a file system which does not support physical persistent storage. 

* It is an on-the-fly file system and exists on RAM.

* It is a kind of **window for the user space to peek the various subsystem of the Linux kernel** such the networking subsystem, memory subsystem, bus interfaces, hardware devices, device drivers, etc.

  Kernel space details are exposed under `/sys` for the user applications.

  ```plain
  debian@beaglebone:/sys$ ls
  block  bus  class  dev  devices  firmware  fs  kernel  module  power
  ```

  Example 1 - Set LED USR3 to a heartbeat sequence, deafault-on, and none.

  ```plain
  debian@beaglebone:/sys$ cd class/leds/beaglebone\:green\:usr3
  debian@beaglebone:/sys/class/leds/beaglebone:green:usr3$ ls
  brightness  device  max_brightness  power  subsystem  trigger  uevent
  
  debian@beaglebone:/sys/class/leds/beaglebone:green:usr3$ echo 'heartbeat' > trigger
  debian@beaglebone:/sys/class/leds/beaglebone:green:usr3$ echo 'default-on' > trigger
  debian@beaglebone:/sys/class/leds/beaglebone:green:usr3$ echo 'none' > trigger
  ```

  > Monitor LED USR3 if it operates accordingly!

* We'll be writing lots of C applications to access peripherals via SYSFS, like LCD, EEPROM, I2C sensor, 7-segment display, SPI, ADC, etc in this course.





## References

Nayak, K. (2022). *Embedded Linux Step by Step Using Beaglebone Black* [Video file]. Retrieved from https://www.udemy.com/course/embedded-linux-step-by-step-using-beaglebone/