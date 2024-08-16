[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C Programming</a> > Using `const` & `volatile` Type Qualifiers Together

# Using `const` & `volatile` Type Qualifiers Together



## Using `const` and `volatile` Together

* Both `const` and `volatile` qualifiers can be used when declaring a variable depending on what you want to achieve. For example,

  ```c
  uint8_t volatile *const pReg = (uint8_t *)0x40000000;
  	// pReg is a const pointer to a volatile data
  	// 1. From pReg's perspective, data in 0x40000000 is not modifiable.
  	// 2. Data in 0x40000000 may undergo unexpected changes so don't optimize read/write 
  	//    operations on this data using pReg.
  
  uint8_t const volatile *const pReg = (uint8_t *)0x40000000;
  	// pReg is a const pointer to a volatile const data
  	// 1. Data pointed to by pReg may undergo unexpected changes, 
  	//	  but the programmer must not change the data present at this address.
  ```

  > L7 - How to interpret `const volatile`?
  >
  > - `const` is for the programmer, not to modify the data using `pReg`.
  > - `volatile` is for the compiler not to optimize the read/write operation on this address.

* Embedded program application:

  Case of reading from read-only buffer or address which is prone to unexpected change of data:

  ```c
  uint8_t const volatile *const pReg = (uint8_t *)0x40000000;
  ```

  > Read-only, so from the programmer's (`pReg`'s) perspective the data must not be modifiable (`const`), but the data can be changed any time by the external world.

  Such a read-only buffer may include "input data register of a peripheral", or a "shared memory from which you are supposed to read-only", etc.



## Usage of `volatile` in an Embedded Program

* When accessing memory-mapped peripheral registers of the microcontrollers

  Following is the code snippet from the "pin_read" exercise.

  ```c
  ...
  uint32_t *pPortAInReg = (uint32_t *)0x40020010;
  ...
  
  while (1)
  {
      // 3. read PA0 input status
      uint8_t pinStatus = (uint8_t)(*pPortAInReg & 0x1);
  
      if (pinStatus)
          *pPortDOutReg |= (1 << 12);
      else
          *pPortDOutReg &= ~(1 << 12);
  }
  ```

  This code works fine with no optimization, but when you do `-O2` optimization the code will not work as intended. This is because the compiler executes L4 only once and then ignores it afterwards. The compiler assumes that `*pPortAInReg` will never change.

  Now, you have to **tell the compiler that the data pointed to by this pointer `pPortAInReg` may change at any time, so not to do any optimization on "data read" and "data write" operations using this pointer**. This can be done by using `volatile` keyword as follows:

  ```c
  uint32_t volatile *const pPortAInReg = (uint32_t *)0x40020010; // 'const' to guard the ptr
  
  // due to the nature of GPIOx IDR, 'const' can be added to the data part as well
  // this is the general way to declare a pointer pointing to a 'read-only' memory mapped registers
  uint32_t const volatile * const pPortAInReg = (uint32_t *)0x40020010;
  ```

  As a rule of thumb, whenever you are accessing memory mapped peripheral registers, use `volatile` generously. Use it for "data", not for the "pointer". For example:

  ```c
  uint32_t volatile *const pClkCtrlReg = (uint32_t *)0x40023830;
  uint32_t volatile *const pPortAModeReg = (uint32_t *)0x40020000;
  uint32_t volatile *const pPortAInReg = (uint32_t *)0x40020010;
  uint32_t volatile *const pPortDModeReg = (uint32_t *)0x40020C00;
  uint32_t volatile *const pPortDOutReg = (uint32_t *)0x40020C14;
  ```

* When a global variable is used to share data between the main code and an ISR code.

  Following is the "button_press_ISR" exercise.

  ```c
  #include <stdint.h>
  #include <stdio.h>
  
  // global shared variable between main code and ISR
  uint8_t volatile g_button_pressed = 0;
  uint32_t g_button_press_count =0;
  
  void button_init(void);
  
  uint32_t volatile *const pEXTTIPendReg		= (uint32_t*) (0x40013C00 + 0x14);
  uint32_t volatile *const pClkCtrlReg		= (uint32_t*) (0x40023800 + 0x30);
  uint32_t volatile *const pClkCtrlRegApb2	= (uint32_t*) (0x40023800 + 0x44);
  uint32_t volatile *const pGPIOAModeReg 		= (uint32_t*) (0x40020000 + 0x00);
  uint32_t volatile *const pEXTIMaskReg 		= (uint32_t*) (0x40013C00 + 0x00);
  uint32_t volatile *const pEXTTIEdgeCtrlReg	= (uint32_t*) (0x40013C00 + 0x08);
  uint32_t volatile *const pNVICIRQEnReg 		= (uint32_t*) 0xE000E100;
  
  int main(void)
  {
  	button_init();
  
  	while(1)
  	{
  		// dissable interrupt
  		*pEXTIMaskReg &= ~( 1 << 0);
  
  		if(g_button_pressed){
  			// some delay until button debouncing gets over
  			for(uint32_t volatile i=0;i<500000/2;i++);
  			g_button_press_count++;
  			printf("Button is pressed : %lu\n",g_button_press_count);
  			g_button_pressed = 0;
  		}
  
  		// enable interrupt
  		*pEXTIMaskReg |= ( 1 << 0);
  	}
  }
  
  void button_init(void)
  {
    *pClkCtrlReg |= (1 << 0);
    *pClkCtrlRegApb2 |= (1 << 14);
    *pEXTTIEdgeCtrlReg |= (1 << 0);
    *pEXTIMaskReg |= (1 << 0);
    *pNVICIRQEnReg |= (1 << 6);
  }
  
  /* button interrupt handler */
  void EXTI0_IRQHandler(void)
  {
  	// set this flag on button press
    g_button_pressed = 1;
  
    *pEXTTIPendReg |= ( 1 << 0);
  }
  ```

  > L27 - If `volatile` keyword is not used in L5, the compiler will assume, when compiled with the higher optimization level, that `g_button_pressed` will always be 0 since nowhere in the code this global variable gets changed. Therefore, make sure to **tell the compiler that  `g_button_pressed` may change at any time by the ISR, so not to do any optimization on "data read" and "data write" operations on that global variable.**
  >
  > L6 - `volatile` not necessary since this global variable does not share data between the main code and the ISR code.
  >
  > L29 - If `volatile` keyword is not used for `i`, the compiler, when compiled with the higher optimization level, is likely to remove (ignore) this loop (i.e., delay for the button press debouncing). This is because the compiler is not aware of our intention and it just thinks that the repetitive operation on `i` is meaningless and redundant (i.e., not affecting any other part of the code) only to slow down the program execution. So, **tell the compiler not to do any optimization on `i`**.
  >
  > Notice that `const` has been used along with `volatile` to prevent the pointers from being changed by accident. 





## References

Nayak, K. (2022). *Microcontroller Embedded C Programming: Absolute Beginners* [Video file]. Retrieved from  https://www.udemy.com/course/microcontroller-embedded-c-programming/
