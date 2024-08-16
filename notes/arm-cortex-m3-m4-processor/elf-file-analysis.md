[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">ARM Cortex-M3/M4 Processor</a> > ELF File Analysis

# ELF File Analysis



## ELF File

* ELF stands for Executable and Linkable Format.
* It is important to be able to analyze the final ELF file to understand various resource allocations. 
* ELF file is essentially a collection of all necessary sections; `.text`, `.data`, `.bss`, etc.
* When doing bare-metal programming, you need to check if all the sections are placed at appropriate absolute addresses.



## Analyzing ELF File

* There are many ways in which you can analyze an ELF file such as using tools like **objdump**, **readelf**, etc.

* You can also instruct the linker to create a special file called the **memory map file** (a.k.a. **map file**). A map file is useful in analyzing various resource allocations and placements in memory.

* To create a map file, you need to add the linker flag `-Map=<filename>.map` when running `arm-none-eabi-gcc` or `arm-none-eabi-ld`.

  Sometimes, linker specific flags may not be recognized when running `arm-none-eabi-gcc`. For such case, prepend `-Wl,` to `-Map=<filename>.map`. (i.e., `-Wl,-Map=<filename>.map`)

* Map file example:

  ```plain
  
  Memory Configuration
  
  Name             Origin             Length             Attributes
  FLASH            0x0000000008000000 0x0000000000100000 xr
  SRAM             0x0000000020000000 0x0000000000020000 xrw 
  *default*        0x0000000000000000 0xffffffffffffffff
  
  Linker script and memory map 
  
  LOAD main.o
  LOAD led.o
  LOAD stm32_startup.o
  
  .text           0x0000000008000000      0x766
   *(.isr_vector)
   .isr_vector    0x0000000008000000      0x188 stm32_startup.o
                  0x0000000008000000                vectors
   *(.text)
   .text          0x0000000008000188      0x4d0 main.o
                  0x0000000008000188                main
                  0x00000000080001b4                idle_task
                  0x00000000080001ba                task1_handler
                  0x00000000080001dc                task2_handler
                  0x00000000080001fe                task3_handler
                  0x000000000800021c                task4_handler
                  0x000000000800023a                init_systick_timer
                  0x00000000080002a8                init_scheduler_stack
                  0x00000000080002b2                init_tasks_stack
                  0x00000000080003cc                update_next_task
                  0x0000000008000454                get_psp_value
                  0x0000000008000474                save_psp_value
                  0x000000000800049c                switch_sp_to_psp
                  0x00000000080004b6                enable_processor_faults
                  0x00000000080004f4                schedule
                  0x0000000008000518                task_delay
                  0x0000000008000578                PendSV_Handler
                  0x000000000800059e                update_global_tick_count
                  0x00000000080005b8                unblock_tasks
                  0x0000000008000618                SysTick_Handler
                  0x0000000008000644                HardFault_Handler
                  0x000000000800064a                MemManage_Handler
                  0x0000000008000650                BusFault_Handler
   .text          0x0000000008000658       0xfc led.o
                  0x0000000008000658                delay
                  0x0000000008000680                led_init_all
                  0x00000000080006f4                led_on
                  0x0000000008000724                led_off
   .text          0x0000000008000754       0x12 stm32_startup.o
                  0x0000000008000754                RTC_Alarm_IRQHandler
                  0x0000000008000754                HASH_RNG_IRQHandler
                  0x0000000008000754                EXTI2_IRQHandler
                  0x0000000008000754                TIM8_CC_IRQHandler
  
  ...
  
                  0x0000000008000754                DMA2_Stream6_IRQHandler
                  0x0000000008000754                DMA1_Stream3_IRQHandler
                  0x000000000800075a                Reset_Handler
   *(.rodata)
                  0x0000000008000766                _etext = .
  
  
  ```

  > * L15: `.text` section begins at 0x08000000 $\to$ correct!
  >
  > * L17: The first object placed in the `.text` section is the vector table and it comes from the file "stm32_startup.o". $\to$ correct!
  >
  > * L20: Next up is the `.text` section of the "main.o" file and its size is 0x4d0.
  >
  > * L44: Next up is the `.text` section of the "led.o"
  >
  > * L60: `.rodata` section is empty since we don't have any constant data in our project.
  >
  > * L61: End of `.text` section is the start of `.data` section. (i.e., `_etext` = `_sdata`)
  >
  > Padding will be introduced by the linker if necessary when the section start address is not word-aligned and will be marked with something like `*fill*`. However, the linker does not care about the word-alignment of the section ending. To make sure that we word-align the end of each section for the section that will come immediately after the current section, use `ALIGN` command.
  >
  > ```plain
  > SECTIONS 
  > { 
  >     .text : 
  >     {
  >         *(.isr_vector) 
  >         *(.text) 
  >         *(.rodata)
  >         . = ALIGN(4); /* assigning 4-byte word-aligned address to the location counter */
  >         _etext = .; 
  >     }> FLASH 
  >  ...
  >  }
  > ```
  >
  > ALIGN(4) forces 4-byte word boundary alignment.





## References

Nayak, K. (2022). *Embedded Systems Programming on ARM Cortex-M3/M4 Processor* [Video file]. Retrieved from  https://www.udemy.com/course/embedded-system-programming-on-arm-cortex-m3m4/
