[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">ARM Cortex-M3/M4 Processor</a> > Thumb State & T Bit of EPSR

# Thumb State & T Bit of EPSR

This section applies to **ARM Cortex M0/M3/M4 Processors**. If you are using any other processors, please consult the corresponding document.



## Thumb State & T Bit of EPSR

* Various ARM processors support ARM-Thumb inter-working, the ability to switch between ARM state and Thumb state.

* The processor must be in **ARM state** to execute instructions which are from **ARM ISA**.

  The processor must be in **Thumb state** to execute instructions of **Thumb ISA**.

* If the T bit of the EPSR is set (1), the processor thinks that the next instruction to execute is from Thumb ISA.

  If the T bit of the EPSR is reset (0), the processor thinks that the next instruction to execute is from ARM ISA.

* The cortex Mx processor does NOT support the ARM state. Hence, the value of T bit must always be 1. Failing to maintain this is illegal and this will result in the "Usage fault" exception.

* The LSB (bit 0) of the program counter (PC) is linked to the T bit. When you load a value (i.e., an address) into PC the bit 0 of the value is loaded into the T bit.

  Hence, any address you place in the PC must have its 0th bit as 1. This is usually taken care of by the compiler and programmers don't have to worry about it most of the time.

  This is why you see all the vector addresses are incremented by 1 (hence, all odd) in the vector table.

  > Question! Does this mean that the addresses of user-defined functions are even by default? Based on the "natural size boundary" of the pointers?
  >
  > $\to$ Yes!
  >
  > Question! When the compiler adds 1 to the even function address to make the LSB 1  for the PC to maintain the T bit value, does the system later decrements the PC address by 1 again to execute the actual function?
  >
  > $\to$ Any branch instruction that writes to PC (Program Counter) only updates  bits 31:1 and forces '0' into the 0th bit. This is how the system works  well.



## Thumb State & T Bit - Demonstration

* The program to demonstrate what the compiler does to the addresses of functions to maintain the T bit (of EPSR)  as 1.

  ```c
  #include <stdint.h>
  #include <stdio.h>
  
  #if !defined(__SOFT_FP__) && defined(__ARM_FP)
    #warning "FPU is not initialized, but the project is compiling for an FPU. Please initialize the FPU before use."
  #endif
  
  /*
   * THREAD MODE	: Privileged/Unprivileged access level
   * HANDLER MODE	: Privileged access level only!
   */
  
  /*
   * This function executes in THREAD MODE of the processor.
   * Within this function, we are triggering software interrupt by accessing the
   * system level registers of the ARM Cortex Mx processor.
   */
  void generate_interrupt()
  {
  	/*
  	 * These are ARM Cortex M4 processor's system control register addresses which
  	 * can only be accessed in PRIVILEGED ACCESS LEVEL.
  	 * Any attempt to change the contents of these registers from being in UNPRIVILEGED
  	 * ACCESS LEVEL will cause a processor fault exception.
  	 */
  	uint32_t *pSTIR = (uint32_t *)0xE000EF00;
  	uint32_t *pISER0 = (uint32_t *)0xE000E100;
  
  	// enable IRQ3 interrupt
  	*pISER0 |= (1 << 3);
  
  	// generate an interrupt from software for IRQ3
  	// (interrupt handler "RTC_WKUP_IRQHandler" will be invoked)
  	*pSTIR = (3 & 0x1FF);
  }
  
  void change_access_level_unpriv(void)
  {
  	/*
  	 * To make the processor transition into UNPRIVILEGED ACCESS LEVEL,
  	 * bit 0 of the CONTROL register must be set to 1.
  	 * CONTROL register is NOT a memory mapped register which means that
  	 * it is impossible for a programmer to access this register using C
  	 * code only. Inline assembly technique is necessary.
  	 */
  	__asm volatile("mrs r0, CONTROL");	// read
  	__asm volatile("orr r0, r0, 0x01");	// modify
  	__asm volatile("msr CONTROL, r0");	// write (this is where access level changes to
  										// UNPRIVILEGED
  }
  
  /*
   * This function executes in THREAD MODE + PRIVILEGED ACCESS LEVEL of the processor.
   * (Remember, after reset, the processor always starts in THREAD MODE.)
   * In reality, "Reset_Handler:" is the first function to be called on reset,
   * but at this point we will assume that the "main" is the first to be called.
   */
  int main(void)
  {
  	printf("Thread mode: before interrupt\n");
  
  	void (*fn_ptr)(void);
  	fn_ptr = change_access_level_unpriv;
  		// Actual address of 'change_access_level_unpriv' is 0x80001e8 (even).
  		// But, the compiler intentionally increments the address by 1 so that the bit 0 of
  		// the PC becomes 1 and that the T bit of EPSR is guaranteed to be 1.
  
  	fn_ptr();	// to dereference the function pointer (to call the function), use ()
  				// at this point, the address of fn_ptr gets loaded into PC
  
  	printf("Thread mode: after interrupt\n");
  
      /* Loop forever */
  	for(;;);
  }
  
  /*
   * This function (ISR) executes in HANDLER MODE of the processor.
   * In HANDLER MODE, you have the full control over the processor. You have the
   * privilege to access any resources you want.
   */
  void RTC_WKUP_IRQHandler(void)
  {
  	printf("Handler mode: ISR\n");
  }
  
  void HardFault_Handler(void)
  {
  	printf("Hard fault detected\n");
  	while(1);
  }
  ```

  > L63 - If you did `fn_ptr = 0x80001e8` (force the original even address), then the execution of L68 will trigger "Hard fault".

  



## References

Nayak, K. (2022). *Embedded Systems Programming on ARM Cortex-M3/M4 Processor* [Video file]. Retrieved from  https://www.udemy.com/course/embedded-system-programming-on-arm-cortex-m3m4/
