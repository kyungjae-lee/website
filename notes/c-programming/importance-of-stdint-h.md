[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C Programming</a> > Importance of `<stdint.h>`

# Importance of `<stdint.h>`



## Data Type size and Compiler

* The size of the data types depends on the compiler.

* Data types may cause portability issues of code when the compiler on which it is compiled changes.

  One compiler may consider integer data type size as 2 bytes, another compiler may consider it as 4 bytes, and some other compiler may consider it as 8 bytes.



## Portability Issues in C Code Due To "Size of Data Types"

* In C programming language the most commonly used data types `int` and `long` cause portability issues. The reason is that the storage size for `int`, and `long` type variables is not defined in the C standard (i.e., C90 or C99).

* Compiler vendors have the choice to define the storage size for the variable depending solely on hardware capabilities of the target platform with respect to the minimum widths defined by the standard.

  For example, the MPLAB XC8 compiler supports 2-byte sized `int`. According to the compiler designer, keeping the size of the `int` type variable as 2 bytes will be most efficient for the data manipulation considering the underlying architecture of the PIC 8-bit microcontrollers.

  The following code demonstrates this compatibility issue:

  ```c
  unsigned int count = 0;
  count++;
  if (count > 65536)
  {
      /* Maximum value of 'count' may be 65,535 or 4,294,967,295 depending on the size of 'int'. 
         With the compiler whose 'int' size is 2 bytes, this section of the code will never get 
         executed */
  }
  ```

* For this reason, in the embedded software world, data types typedef'd in the standard library header file `<stdint.h>` are used instead of the standard data types.



## `<stdint.h>`

* The standard library header file `<stdint.h>` defines fixed-width integers using alias data types for the standard data types available in C.

* A fixed-width integer data type is an aliased data type that is based on the exact number of bits required to store the data.

* `<stding.h>` helps you choose an exact size for your variable and makes code portable no matter which compiler the code may be compiled on.

* Example:

  ```c
  /* stdint.h - MinGW installed on Windows X86 64-bit system */
  typedef signed char		int8_t;
  typedef unsigned char	uint8_t;
  typedef short			int16_t;
  typedef unsigned short	uint16_t;
  ...
  ```

  Note that these are not new data types, these are just aliased names to the standard data types in C. Each compiler carries its own `<stdint.h>` which needs to be included in the project for these data types to be used. (Check your compiler folder!)

* Using `<stdint.h>` data types, compilers will always reserve 32 bits for the variable `uint32_t` by using an appropriate standard data type. The following code is now portable!

  ```c
  uint32_t count = 0;
  count++;
  if (count > 65536)
  {
      /* task to be done */
  }
  ```

* Some useful `<stdint.h>` aliases when programming on an unknown architecture:

  ```c
  uintmax_t	/* defines the largest fixed-width unsigned integer possible on the system */
  intmax_t	/* defines the largest fixed-width signed integer possible on the system */
  uintptr_t	/* defines an unsigned integer type that is wide enough to store the pointer value */
  ```

  > Do not need to worry about the what the size of the pointer is and so on. The compiler's `<stdint.h>` file will map it to an appropriate standard data type according to the underlying hardware architecture.





## References

Nayak, K. (2022). *Microcontroller Embedded C Programming: Absolute Beginners* [Video file]. Retrieved from  https://www.udemy.com/course/microcontroller-embedded-c-programming/
