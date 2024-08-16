[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C Programming</a> > `volatile` Type Qualifier

# `volatile` Type Qualifier



## `volatile` Type Qualifier

`volatile` is a type qualifier in C used with variables to **instruct the compiler not to perform any optimization** on the variable operation.

It tells the compiler that the value of the variable may change at any time with/without the programmer's consent. Even with the higher optimization level (> `-O0`) setting, the compiler will turn off the optimization of the read/write operations on those variables that are declared using `volatile` type qualifier.

`volatile` is very helpful in embedded systems code.



## When to Use `volatile` Type Qualifier?

A variable must be declared using a `volatile` type qualifier when there is a possibility of unexpected changes (e.g., by user) in the variable value.

The unexpected changes in the variable value may happen from within the code or from outside the code (i.e., from the hardware).

Use `volatile` when your code is dealing with the following scenarios:

* Memory-mapped peripheral registers of the microcontrollers
* Multiple tasks accessing global variables (read/write) in an RTOS multi-threaded application
* When a global variable is used to share data between the main code and an ISR code



## Usage of `volatile` Type Qualifier

### Case 1: `volatile` data

**Syntax:**

```c
volatile uint8_t data;	
uint8_t volatile data;	/* data is a volatile uint_t type variable */
```

> Second notation is preferred by many programmers because it's easier to interpret when it gets complicated.

### Case 2: Non-`volatile` pointer to `volatile` data

**Syntax:**

```c
uint8_t volatile *pStatusReg;	/* pStatusReg is a (non-volatile) pointer to a volatile uint8_t type data */
								/* instructs the compiler not to do any optimization on data read/write 
								   operations using pStatusReg */
```

Widely used when accessing memory-mapped registers. Use this syntax generously whenever you are accessing memory-mapped registers in your microcontroller code.

### Case 3: `volatile` pointer to non-`volatile` data

**Syntax:**

```c
uint8_t *volatile pStatusReg;	/* pStatusReg is a volatile pointer to a (non-volatile) uint8_t type data */
```

Rarely used.

### Case 4: `volatile` pointer to `volatile` data

**Syntax:**

```c
uint8_t volatile *volatile pStatusReg;	/* pStatusReg is a volatile pointer to a volatile uint8_t type data */
```

Rarely used.





## References

Nayak, K. (2022). *Microcontroller Embedded C Programming: Absolute Beginners* [Video file]. Retrieved from  https://www.udemy.com/course/microcontroller-embedded-c-programming/
