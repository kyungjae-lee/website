[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C Programming</a> > Pointers

# Pointers



## Pointers in C

* Pointers are one of the essential programming features which are available in C.
* Pointers make C programming more powerful.
* Pointers are heavily used in embedded C programming to
  * Configure the peripheral register addresses
  * Read/write into peripheral data registers
  * Read/write into SRAM/FLASH locations and for many other things.



## Address (`&var`) is not a Regular Number!

*  In C, an address of a variable is not a regular number. Consider the following example:

  ```c
  /* assume 64-bit system, size of a pointer is 8 byte */
  char ch = 'A';
  unsigned long long int addr_ch = &ch;	// size of 'long long in' is always 8 bytes 
  ```

  > Compiler will give you warning, because `&ch` is not just a number but is recognized as a **pointer** which is of type `char *`.

  If you still want to make this work, **typecast** the pointer into `unsigned long long int`.

  ```c
  unsigned long long int addr_ch = (unsigned long long int)&ch;
  ```




## Pointer Operators

* `*`: Dereference (or "value at address") operator
* `&`: "Address of" operator



## What Significance does the Pointer Data Type Have?

* The pointer data type decides the behavior of the operations carried out on the pointer variable.

  Operations: read, write, increment, decrement

### Examples

* Read operations using pointers

  ```c
  /* Read operation on variable 'address1' yields 1 byte of data */
  char *address1 = (char *)0x00007FFF8E3C3824;
  /* '*address1' -> 1 byte of data
  ```

  > The pointer data type `char *` decides how many byte(s) of data can be read when using that pointer.
  >
  > In this case `address1` is called a "pointer variable to a `char` type data".

  ```c
  /* Read operation on variable 'address1' yields 4 byte of data */
  int *address1 = (int *)0x00007FFF8E3C3824;
  ```

  ```c
  /* Read operation on variable 'address1' yields 8 byte of data */
  long long int *address1 = (long long int *)0x00007FFF8E3C3824;
  ```

* Write operations using pointers

  ```c
  char *address1 = (char *)0x00007FFF8E3C3824;
  *address1 = 0x89;	/* dereferencing a pointer to write data */
  ```

  

## Pointer Usage in Embedded Programming

* Store data into required SRAM locations.
* For copying data from peripheral register to SRAM memory and vice versa.
* To configure the peripheral registers. Because peripheral registers are memory-mapped and each register will be given unique address in the MCU memory map.
* Pointers to ISRs are stored in vector table to handle the interrupts.
* Pointers are also used to configure the memory-mapped processor specific registers like interrupt configuration registers.
