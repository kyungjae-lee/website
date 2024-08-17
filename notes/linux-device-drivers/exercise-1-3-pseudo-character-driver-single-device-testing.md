[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Linux Device Drivers</a> > Exercise 1-3: Pseudo Character Driver (Single Device) - Testing

# Exercise 1-3: Pseudo Character Driver (Single Device) - Testing



## On the Host

1. Build the kernel module `pcd.c` against the host Linux kernel version

2. Insert the mode: `sudo insmod pcd.ko`

3. Test the pseudo character driver: `echo "Hello, welcome!" > /dev/pcd` $\to$ `dmesg`

   Output:

   ```plain
   [21343.359074] pcd_driver_init :Device number <major>:<minor> = 509:0
   [21343.359135] pcd_driver_init :Module init was successful
   [21401.491786] pcd_open :open was successful
   [21401.491856] pcd_write :Write requested for 16 bytes
   [21401.491863] pcd_write :Current file position = 0
   [21401.491868] pcd_write :16 bytes successfully written
   [21401.491871] pcd_write :Updated file position = 16
   [21401.491901] pcd_release :close was successful
   ```

   > `echo` command opens the pseudo character device by invoking the `open()` system call. (L5: `pcd_open()` gets called from the driver)
   >
   > L7: When a file is opened, the current file position is always 0.
   >
   > L10: `echo` invokes `close()` system call, and subsequently `pcd_release()` of the driver gets called.

4. Check the contents of the device (buffer): `cat /dev/pcd`

   Output:

   ```plain
   Hello, welcome!
   ```

5. Check the read activity: `dmesg | tail`

   ```plain
   [22741.144956] pcd_open :open was successful
   [22741.144967] pcd_read :Read requested for 131072 bytes
   [22741.144969] pcd_read :Current file position = 0
   [22741.144971] pcd_read :512 bytes successfully read
   [22741.144972] pcd_read :Updated file position = 512
   [22741.144980] pcd_read :Read requested for 131072 bytes
   [22741.144981] pcd_read :Current file position = 512
   [22741.144982] pcd_read :0 bytes successfully read
   [22741.144983] pcd_read :Updated file position = 512
   [22741.144991] pcd_release :close was successful
   ```

   > L2: Read request for 131072 bytes because that's how the `cat` program is implemented!
   >
   > L5: Updated file position is 512 bytes (i.e., `DEV_MEM_SIZE`) because that's the size of our buffer.
   >
   > L6: `cat` requests to read again
   >
   > L8: 0 bytes since no more to read

6. Attempt copying a large text file (> 512 Bytes) into `/dev/pcd`: `cp file.txt /dev/pcd`

   Output:

   ```plain
   cp: error writing '/dev/pcd': Cannot allocate memory
   ```

   And run `dmesg` to see what happened:

   ```plain
   [24039.217897] pcd_driver_init :Module init was successful
   [24045.901773] pcd_open :open was successful
   [24045.901788] pcd_write :Write requested for 1717 bytes
   [24045.901790] pcd_write :Current file position = 0
   [24045.901792] pcd_write :512 bytes successfully written
   [24045.901793] pcd_write :Updated file position = 512
   [24045.901795] pcd_write :Write requested for 1205 bytes
   [24045.901796] pcd_write :Current file position = 512
   [24045.901797] pcd_write :No more space left on the device
   [24045.901973] pcd_release :release was successful
   ```

   > L6: 512 bytes successfully written
   >
   > L7: The file position reached the end
   >
   > L8: `cp` command attempts to copy the rest of the contents (1717 - 512 = 1205 bytes)
   >
   > L9: Since the file position is already at the end, no more write can be done. (`pcd_write()` returns the error code `-ENOMEM` which triggers the error message `cp: error writing '/dev/pcd': Cannot allocate memory`.)
