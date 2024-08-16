[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C Programming</a> > Optimization

# Optimization



## Optimization

Optimization is a series of actions taken by the compiler on your program's code generation process to reduce:

* Number of instructions (code space optimization)
* Memory access time (time space optimization)
* Power consumption

By default, the compiler doesn't invoke any optimization on your program (i.e., Optimization level 0; `-O0`). Optimization can be enabled by using the compiler optimization flags.



## Optimization Levels (GCC Compiler Flags)

You need to work with different optimization levels to find out what works best for your situation.

### `-O0` 

* **No optimization (default)**

* Not recommended for productions if you have limited CODE and RAM space
* Has fastest compilation time
* Debugging friendly and used during development
* Code that works with `-O0` optimization level may not work with higher optimization levels. Code needs to be verified again when it is compiled with different optimization level.
* Note that many other compilers do substantial optimization even if "no optimization" is specified. With gcc, it is very unusual to use `-O0` for production if execution time is of any concern, since `-O0` means (almost) no optimization. This difference between gcc and other compilers should be kept in mind when doing performance comparisons.

### `-O1` 

* **Moderate optimization**

* Optimizes reasonably well (to decrease memory access time and code space) but does not degrade compilation time significantly

### `-O2` 

* **Full optimization**

* Generates highly optimized code and has the slowest compilation time
* Not debugging friendly

### `-O3`

* **Full optimization as in `-O2` + some more aggressive optimization steps taken by the compiler**

* Has slowest compilation time
* May cause bugs in the program
* Not debugging friendly

### `-Os`

* Optimizes space usage (code and data) of resulting program.



## Effect of Optimization

* No optimization (`-O0`)

  ```plain
  arm-none-eabi-size   pin_read.elf 
     text	   data	    bss	    dec	    hex	filename
      820	      8	   1568	   2396	    95c	pin_read.elf
  Finished building: default.size.stdout
  ```

  > text size = 820

* `-01`

  ```plain
  arm-none-eabi-size   pin_read.elf 
  arm-none-eabi-objdump -h -S  pin_read.elf  > "pin_read.list"
     text	   data	    bss	    dec	    hex	filename
      724	      8	   1568	   2300	    8fc	pin_read.elf
  Finished building: default.size.stdout
  ```

  > text size = 724

* `-03`

  ```plain
  arm-none-eabi-size   pin_read.elf 
  arm-none-eabi-objdump -h -S  pin_read.elf  > "pin_read.list"
     text	   data	    bss	    dec	    hex	filename
      700	      8	   1568	   2276	    8e4	pin_read.elf
  Finished building: default.size.stdout
  ```

  > text size = 700
