[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">ARM Cortex-M3/M4 Processor</a> > Semihosting

# Semihosting



## Semihosting

* Semihosting is a mechanism that enabels code running on an ARM target to communicate and use the input/output facilities on a host computer that is running a debugger.

* Examples of these facilities include keyboard input, screen output, and disk I/O.

* To use semihosting feature, the host, OpenOCD in our case, must be running already. 

* The `printf()` message will be pulled by the host application (i.e., OpenOCD), and then will be displayed on the OpenOCD console. That is why OpenOCD must be running and should be in connection with the target hardware.

* To use the semihosting feature, you just need to specify the spec: `--specs=rdimon.specs`.

  Newlib-Nano (`libc_nano.a`) was used when the specified spec was `--specs=nano.specs`.



<img src="./img/locating-newlib-and-newlib-nano.png" alt="locating-newlib-and-newlib-nano" width="800">



* Also, in the main application of your project, you need to initialize the semihosting feature.

  ```c
  /* main application */
  extern void initialise_monitor_handles(void);
  ...
  initialise_monitor_handles();	/* this function must be called */
  ...
  ```

  > `initialise_monitor_handles()` initializes routines and libraries that are necessary for semihosting.

* When using semihosting feature, make sure that you are not linking `syscalls.o` that you wrote on your own. Now, the semihosting libraries will be providing the low-level system calls. So, if it is still included in your makefile dependency/recipie, exclude it from building.

* Error resolution:

  I got the following error.

  ```plain
  $ make sh
  
  arm-none-eabi-gcc -c -mcpu=cortex-m4 -mthumb -mfloat-abi=soft -std=gnu11 -Wall -o0 -o main.o main.c	# recipie: command to generate the target
  # arm-none-eabi-gcc -c -mcpu=cortex-m4 -mthumb -mfloat-abi=soft -std=gnu11 -Wall -o0 main.c -o main.o
  # arm-none-eabi-gcc -c -mcpu=cortex-m4 -mthumb -mfloat-abi=soft -std=gnu11 -Wall -o0 main.c -o main.o
  # 'main.c' represents dependency, 'main.o' represents target (@ does look like a target :))
  arm-none-eabi-gcc -c -mcpu=cortex-m4 -mthumb -mfloat-abi=soft -std=gnu11 -Wall -o0 -o led.o led.c 
  arm-none-eabi-gcc -c -mcpu=cortex-m4 -mthumb -mfloat-abi=soft -std=gnu11 -Wall -o0 -o stm32_startup.o stm32_startup.c 
  arm-none-eabi-gcc -mcpu=cortex-m4 -mthumb -mfloat-abi=soft --specs=rdimon.specs -T stm32_ls.ld -Wl,-Map=final.map -o final_sh.elf main.o led.o stm32_startup.o 
  /usr/lib/gcc/arm-none-eabi/10.3.1/../../../arm-none-eabi/bin/ld: /usr/lib/gcc/arm-none-eabi/10.3.1/../../../arm-none-eabi/lib/thumb/v7e-m/nofp/rdimon-crt0.o: in function `_mainCRTStartup':
  /build/newlib-pB30de/newlib-3.3.0/build/arm-none-eabi/thumb/v7e-m/nofp/libgloss/arm/../../../../../../../libgloss/arm/crt0.S:547: undefined reference to `__end__'
  collect2: error: ld returned 1 exit status
  make: *** [makefile:35: final_sh.elf] Error 1
  ```

  > It means that the semihosting needs `__end__` instead of what we have (`end`). Define it in the `.bss` section.

* When using `printf()` to display any message with semihosting, make sure to end your message with `\n` character to specify the end of the string. This is required for semihosting to know where the string ends.



## Semihosting Procedure

1. Connect your board to the host machine.

2. Run OpenOCD with the board configuration file

   ```plain
   openocd -f /board/stm32f4discovery.cfg
   ```

   > Full path to the board configuration file: `\usr\share\openocd\scripts\board\stm32f4discovery.cfg`
   >
   > Leave this terminal busy, and launch another terminal. Go to project directory and do Step 3.

3. Connect to the OpenOCD via GDB Client or Telnet Client 

   **Using GDB Client:**

   ```plain
   gdb-multiarch
   (gdb) target remote localhost:3333
   (gdb) monitor reset init
   ```

   > Keyword `monitor` allows you to issue OpenOCD command on GDB client. (This keyword is not necessary if you are issuing OpenOCD command on Telnet.)

   **Using Telnet**

   ```plain
   telnet localhost 4444
   reset init
   ```

   > You are not bypassing GDB Client, so `monitor` keyword is not necessary. Just directly type in commands.
   >
   > c.f. To terminate Telnet session, `Ctrl+]` $\to$ `close`.

4. Issue commands over GDB Client or Telnet to OpenOCD to download and debug the code.

   ```plain
   (gdb) monitor flash write_image erase final_sh.elf
   ```

   ```plain
   flash write_image erase final_sh.elf
   ```

5. Enable the semihosting feature in the OpenOCD

   ```plain
   (gdb) monitor arm semihosting enable
   ```

   ```plain
   arm semihosting enable
   ```

   



## References

Nayak, K. (2022). *Embedded Systems Programming on ARM Cortex-M3/M4 Processor* [Video file]. Retrieved from  https://www.udemy.com/course/embedded-system-programming-on-arm-cortex-m3m4/
