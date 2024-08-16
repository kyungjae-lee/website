[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C Programming</a> > Preprocessor Directives

# Preprocessor Directives



## Introduction to Preprocessor Directives

* In C programming preprocessor directives are used to affect compile-time settings.

* Preprocessor directives are also used to create macros used as a textual replacement for numbers and other things.

* Preprocessor directives begin with the `#` symbol.

* Preprocessor directives are resolved or taken care of during the preprocessing stage of compilation.


* Preprocessor Directives Supported in C:

  | Objective               | Usage                                              | Syntax                                                       |
  | ----------------------- | -------------------------------------------------- | ------------------------------------------------------------ |
  | Macros                  | Used for textual replacement                       | `#define <identifier> <value>`                               |
  | File inclusion          | Used for file inclusion                            | `#include <std_lib_filename>` <br>`#include "user_defined_filename"` |
  | Conditional compilation | Used to direct the compiler about code compilation | `#ifdef`, `#endif`, `#if`, `#else`, `#ifndef`, `#undef`      |
  | Others                  |                                                    | `#error`, `#pragma`                                          |



## Macros (`#define`)

* Macros are written in C using `#define` preprocessor directive.

* Macros are used for **textual replacement** in the code, commonly used to define constants.

* Syntax:

  ```c
  #define <identifier> <value>	/* note that there is NO ';' at the end */
  ```

  Example:

  ```c
  #define MAX_RECORD 10
  ```

* During the preprocessing stage of the compilation process, macro names (identifiers; NOT variable names) are replaced by the associated values inside the program.

* In embedded systems programming (in C), macros are frequently used to define pin numbers, pin values, crystal speed, peripheral register addresses, memory addresses and for other configuration values. 

  For example,

  ```c
  #define PIN_8				8
  #define GREEN_LED			PIN_8
  #define LED_STATE_ON		1
  #define LED_STATE_OFF		0
  #define XTAL_SPEED			8000000UL		/* UL stands for unsigned long */
  #define	FLASH_BASE_ADDR		0x08000000UL
  #define SRAM_BASE_ADDR		0x20000000ul
  ```

  By convention, macro names are defined in uppercase letters to distinguish them from the variable names which are generally defined using camel-case or snake-case.

* **Function-like macros:**

  To define a function-like macro, use the same `#define` directive, but put a pair of parentheses immediately after the macro name. For example:

  ```c
  #define PI_VALUE 3.1415
  #define AREA_OF_CIRCLE(r) PI_VALUE * r * r
  
  areaCircle = AREA_OF_CIRCLE(radius);	/* original statement */
  ```

  Preprocessor first perform the textual replacement to yield

  ```c
  areaCircle = PI_VALUE * radius * radius;	/* textual replacement step 1 */
  ```

  then,

  ```c
  areaCircle = 3.1415 * radius * radius;		/* textual replacement step 2 */
  ```

  Be extra careful with the macro values (i.e., the combination of operands and operators) when you are doing some operations using multiple operands. Safety measure is to put parentheses around each and every operand, plus the whole expression. Imagine what could possibly happen without! The following is the correct format:

  ```c
  #define AREA_OF_CIRCLE(r) ((PI_VALUE) * (r) * (r))
  ```

* Summary

  * Use meaningful macro names
  * It is recommended that you use UPPERCASE letters for macro names to distinguish them from variables.
  * Macro names are NOT variables. They are labels or identifiers, and they don't occupy any code space or RAM space during the compile-time or run-time of the program.
  * Make sure to put parentheses around each and every operand, plus the whole expression.

### Usage of Macros in an Embedded Program

* Toggling LED program can be abstracted one step further by applying macros.

  Before (without macros):

  ```c
  // toggle_led.c
  
  int main(void)
  {
  	RCC_AHB1ENR_t volatile *const pClkCtrlReg = (RCC_AHB1ENR_t *)0x40023830;
  	GPIOx_MODER_t volatile *const pPortDModeReg = (GPIOx_MODER_t *)0x40020C00;
  	GPIOx_ODR_t volatile *const pPortDOutReg = (GPIOx_ODR_t *)0x40020C14;
  
  	// 1. enable the clock for GPOID peripheral in the AHB1ENR (SET the 3rd bit position)
  	pClkCtrlReg->gpiod_en = 1;
  
  	// 2. configure the mode of the IO pin as output
  	//    make 24th bit position as 1 (SET)
  	pPortDModeReg->pin_12 = 1;
  
  	while(1)
  	{
  		// 3.SET 12th bit of the output data register to make I/O pin-12 as HIGH
  		pPortDOutReg->pin_12 = 1;
  
  		// introduce small human observable delay
  		// This loop executes for 10K times
  		for(uint32_t i=0 ; i < 300000 ; i++ );
  
  		// Tun OFF the LED
  		pPortDOutReg->pin_12 = 0;
  
  		for(uint32_t i=0 ; i < 300000 ; i++ );
  	}
  }
  ```

  After (with macros):

  ```c
  // toggle_led.h
  
  // macros
  #define ADDR_REG_AHB1ENR ((RCC_AHB1ENR_t *)0x40023830)
  #define ADDR_REG_GPIOD_MODE ((GPIOx_MODER_t *)0x40020C00)
  #define ADDR_REG_GPIOD_OD ((GPIOx_ODR_t *)0x40020C14)
  
  #define CLOCK_ENABLE (1)
  #define MODE_CONF_OUTPUT (1)
  #define PIN_STATE_HIGH (1)
  #define PIN_STATE_LOW (0)
  
  #define DELAY_COUNT 300000UL	// unsigned long 32-bit value
  ```

  ```c
  // toggle_led.c
  
  int main(void)
  {
  	RCC_AHB1ENR_t volatile *const pClkCtrlReg = ADDR_REG_AHB1ENR;
  	GPIOx_MODER_t volatile *const pPortDModeReg = ADDR_REG_GPIOD_MODE;
  	GPIOx_ODR_t volatile *const pPortDOutReg = ADDR_REG_GPIOD_OD;
  
  	// 1. enable the clock for GPOID peripheral in the AHB1ENR (SET the 3rd bit position)
  	pClkCtrlReg->gpiod_en = CLOCK_ENABLE;
  
  	// 2. configure the mode of the IO pin as output
  	//	  make 24th bit position as 1 (SET)
  	pPortDModeReg->pin_12 = MODE_CONF_OUTPUT;
  
  	while(1)
  	{
  		// 3.SET 12th bit of the output data register to make I/O pin-12 as HIGH
  		pPortDOutReg->pin_12 = PIN_STATE_HIGH;
  
  		// introduce small human observable delay
  		// This loop executes for 10K times
  		for(uint32_t i=0 ; i < DELAY_COUNT ; i++ );
  
  		// Tun OFF the LED
  		pPortDOutReg->pin_12 = PIN_STATE_LOW;
  
  		for(uint32_t i=0 ; i < DELAY_COUNT ; i++ );
  	}
  }
  ```

  > Now, the program has become more intuitive to read!

​	

## Conditional Compilation Using Preprocessor Directives

* Conditional compilation directives (e.g., `#if`, `#ifdef`, `#endif`, `#else`, `elif`, `#undef`, `#ifndef`) help include or exclude individual code blocks based on various conditions set in the program.

### `#if` and `#endif` Directives

* Syntax:

  ```c
  #if <constant expression>	/* e.g., #if 0 */
  	/* code block */
  #endif
  ```

* This directive checks whether the constant expression is zero or non-zero value. If constant is 0, then the code block will NOT be included for the code compilation. If constant is non-zero, the code block will be included for the code compilation.

* `#endif` directive marks the end of scope of `#if`, `#ifdef`, `#ifndef`, `#else`, `#elif` directives.

* These directives can be nested:

  ```c
  #if 1
  	/* code to be included */
  #if 0
  	/* code to be excluded */
  #endif
  #endif
  ```

* Also, conditional inclusion can be achieved by using `#else`:

  ```c
  #if 0
  	/* code block 1 */
  #else
  	/* code block 2 */
  #endif
  ```

  By setting the constant expression value of `#if`, we can include either the code block 1 or 2.

### `#ifdef` and `#ifndef` Directives

* Syntax:

  ```c
  #ifdef <identifier> /* e.g., #ifdef NEW_FEATURE */
  	/* code block */
  #endif
  ```

  `#ifdef` directive checks whether the identifier is defined in the program or not. Only if the identifier is defined, the code block will be included for compilation.

* Exercise:

  1. Execute triangle area calculation code block only if `AREA_TRI` macro is defined in the program.
  2. Execute circle area calculation code block only if `AREA_TRI` is not defined.

  ```c
  #include <stdio.h>
  
  /* AREA_TRI, AREA_CIR can be defined/undefined here.
  #define AREA_TRI
  #define AREA_CIR
  #undef AREA_CIR
  */
  
  int main(int argc, char *argv[])
  {
  #ifdef AREA_TRI
      /* code block to calculate triangle area */
  #endif
  
  #ifndef AREA_TRI
      /* code block to calculate circle area */
  #endif 
      
      return 0;
  }
  ```

  Note that instead of including `#define AREA_TRI` in the code, you can also pass `AREA_TRI` as an argument to the compiler using `-D` flag when compiling this program. e.g., `gcc -DAREA_TRI <filename>`.

### `#ifdef` and `#else` Directives

* Syntax and example:

  ```c
  #ifdef NEW_FEATURE
  	/* code block of new feature */
  #else
  	/* code block for old feature */
  #endif
  ```

### `#if` Directive and `defined` Operator

* The `defined` operator is used when you want to check definitions of multiple macros using single `#if`, `#ifdef` or `#ifndef` directives.

* You can also use C logical operators such as `AND`, `NOT`, `OR` with `defined` operator. For example:

  ```c
  #ifdef AREA_CIR
  	#ifdef AREA_TRI
  		/* code block to be included only if both AREA_CIR and AREA_TRI are defined */
  	#endif
  #endif
  ```

  can be rewritten as:

  ```c
  #if defined(AREA_CIR) && defined(AREA_TRI)
  	/* code block to be included only if both AREA_CIR and AREA_TRI are defined */
  #endif
  ```

### `#error` Directiv

* `#error` terminates the compilation with error message:

  ```c
  #if !defined(AREA_CIR) && !defined(AREA_TRI)
  	#error "No macros defined."
  #endif
  ```

  This code can be placed anywhere necessary in the program. (e.g., Outside the `main()`, inside the `main()`, etc.)

* `#warning` does NOT terminate the compilation but does give the warning message:

  ```c
  #if !defined(AREA_CIR) && !defined(AREA_TRI)
  	#warning "No macros defined."
  #endif
  ```

  



## References

Nayak, K. (2022). *Microcontroller Embedded C Programming: Absolute Beginners* [Video file]. Retrieved from  https://www.udemy.com/course/microcontroller-embedded-c-programming/
