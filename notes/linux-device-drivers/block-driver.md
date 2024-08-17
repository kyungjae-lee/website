[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Linux Device Drivers</a> > Block Driver

# Block Driver



## Block Driver

* A Linux **block device** is a type of device that allows data to be read  from or written to in fixed-sized blocks, typically used for storage  devices like hard drives or solid-state drives.
* A Linux **block driver** is a software component that facilitates communication and control of block devices, handling the read and write  operations of fixed-sized blocks of data between the operating system and storage devices.
* Block drivers are more complicated than char drivers because they should implement advanced buffering strategies to read from and write to the block devices, which involves disk caches.
* Examples: Mass storage devices such as hard disks, SDMMC, NAND Flash, USB camera, etc.
