[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">ARM Cortex-M3/M4 Processor</a> > Reset Sequence

# Reset Sequence



## Reset Sequence

1. When you reset the processor (e.g., press the reset button on the boad), the PC is loaded with the value 0x0000_0000. (Memory location 0x0000_0000 belongs to a Flash memory, or ROM memory, or Program memory.)

2. Then the processor reads the value at the memory location 0x0000_0000 into the MSP.

   - MSP = value at 0x0000_0000
   - MSP is the **Main Stack Pointer** register. This means that the processor, upon reset, first initializes the Stack Pointer.

3. Then the processor reads the value at the memory location 0x000_0004 into PC. That value is actually the address of the **reset handler**. (In other words, the address of the reset handler gets stored into PC.)

4. PC jumps to the reset handler.

   A reset handler is just a C or assembly function written by you to carry out any initialization required.

5. From the reset handler, `main()` function of the application gets called.

This is how the control reaches your program's `main()` function after reset.



## Reset Handler



<img src="./img/structure-of-reset-handler.png" alt="structure-of-reset-handler" width="800">



* Reset handler does the early initialization after which the call to the user program (`main()`) is made:

  Processor reset 

  $\to$ data section initialization 

  $\to$ BSS section initialization 

  $\to$ initialization of a standard C library if it is used (e.g., `__libc_init_array();`)

  $\to$ calls the user program (`main()`)

* Remember! The `main()` function cannot be called without these important early initializations.

  ```assembly
  /* a section of startup file (startup_stm32f407vgtx.s) */
  
  Reset_Handler:
    ldr   r0, =_estack
    mov   sp, r0          /* set stack pointer */
  /* Call the clock system initialization function.*/
    bl  SystemInit
  
  /* Copy the data segment initializers from flash to SRAM */
    ldr r0, =_sdata
    ldr r1, =_edata
    ldr r2, =_sidata
    movs r3, #0
    b LoopCopyDataInit
  
  CopyDataInit:
    ldr r4, [r2, r3]
    str r4, [r0, r3]
    adds r3, r3, #4
  
  LoopCopyDataInit:
    adds r4, r0, r3
    cmp r4, r1
    bcc CopyDataInit
  
  /* Zero fill the bss segment. */
    ldr r2, =_sbss
    ldr r4, =_ebss
    movs r3, #0
    b LoopFillZerobss
  
  FillZerobss:
    str  r3, [r2]
    adds r2, r2, #4
  
  LoopFillZerobss:
    cmp r2, r4
    bcc FillZerobss
  
  /* Call static constructors */
    bl __libc_init_array
  /* Call the application's entry point.*/
    bl main
  ```

  



## References

Nayak, K. (2022). *Embedded Systems Programming on ARM Cortex-M3/M4 Processor* [Video file]. Retrieved from  https://www.udemy.com/course/embedded-system-programming-on-arm-cortex-m3m4/
