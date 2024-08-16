[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C Programming</a> > `const` Type Qualifier

# `const` Type Qualifier



## `const` Type Qualifier

`const` is a type qualifier in C used to **enforce read-only feature** on variables. It adds some safety to your code. The compiler will warn you when you try to modify the value of the `const` variable.

```c
uint8_t data1 = 10;		/* data1 is called a variable */
data1 = 50;				/* OK; data1's value can be modified throughout the program */
```

```c
const uint8_t data2 = 10;	/* data2 is called a constant (read-only) variable */
data2 = 50;					/* compile-time error; data2's value cannot be modified */
```

Use `const` generously to enforce pointer access restrictions while writing functions and function prototypes.

Using `const` may also help the compiler generate the optimized code.



## How to Use `const` Type Qualifier?

**Syntax**:

```c
const uint8_t data1 = 10; 	
uint8_t const data1 = 10;	/* data1 is a constant (read-only) variable of type uint8_t */
```

Some programmers prefer the second way because it helps interpret a complex statement better (i.e., Reading  backwards). First, identify the variable name, then read from left to right.



## `const`ness of a Variable

By using the `const` keyword, you (as a programmer) are just making a promise to the compiler that you won't try to modify the content of the variable using its **name**. 

If you try to modify the variable by its name, the compiler will stop you by throwing an error (i.e., compile-time error).

```c
const uint8_tdata = 10;
data = 50;		/* compile-time error */
```

However, you can still modify the content of the variable by using a pointer of an appropriate type that points to that variable.

```c
const uint8_t data = 10;
uint8_t *ptr = (uint8_t*)&data;	/* ptr does not know anything about the constness of data */
*ptr = 50;		/* OK */
```

`const` does NOT mean that the value will never change. It is just a programming safety feature to prevent the programmer from modifying the value in a certain way.



## Placement of `const` Variables in Memory

All local `const` variables are just like non-`const` variables as far as memory placement is concerned. They are placed in RAM. The only specialty of a `const` variable is that it is read-only.

All global `const` variables are stored in ROM or FLASH. This also further depends on the linker script rules and the hardware on which the code runs. 

For example, in STM32 target hardware, all global `const` variables reside in the FLASH memory. So, when you try to modify the `const` variable using its address, the operation will have no effect because the FLASH memory of the microcontroller is write-protected.

```c
 const uint8_t data = 10;	/* global const variable (stored in ROM or FLASH) */

int main(int argc, char *argv[])
{
    uint8_t *ptr = (uint8_t*)&data;	
	*ptr = 50;	/* run-time error (e.g., segmentation fault)
				   programmers are not allowed to change the data stored in the read-only memory
				   section by using a pointer in the code */
}
```



## Usage of `const` Type Qualifier

### Case 1: Constant data

**Syntax:**

```c
const uint8_t data = 50;	/* data is read-only */

data = 10;		/* not allowed */
```

**Use case:**

To define mathematical constants in the program

```c
const float pi = 3.14;
const float fadius = 4;
const int number_of_months = 12;
```

### Case 2: Modifiable pointer to constant data

**Syntax:**

```c
const uint8_t *pData = (uint8_t*)0x40000000; /* pData is a pointer to a read-only data */

pData = (uint8_t*)0x50000000;	/* allowed */
*pData = 50;					/* not allowed */
```

Here, the pointer `pData` is modifiable, but **from `pData`'s perspective** the memory location it points to is not modifiable (read-only). Note that this does not necessarily mean that the memory location itself is non-modifiable in nature. It's just that `pData` recognizes it that way because of the way `pData` is declared.

**Use case:**

In the following function, `src` is guarded to prevent the data pointed to by `src` from being modified by mistake.

```c
/* copies data from src to dst */
void copy_src_to_dst(const uint8_t *src, uint8_t *dst, uint32_t len)
{
    for (uint32_t i = 0; i < len; i++)
    {
    	*dst++ = *src++;
    }
}
```

This usage can be found in many system call declarations:

```c
int open(const char *path, int oflag, ...);
ssize_t write(int fd, const void *buf, size_t count);
```

### Case 3: Constant pointer to modifiable data

**Syntax:**

```c
uint8_t *const pData = (uint8_t*)0x40000000; /* pData is a constant pointer to a modifiable data */

pData = (uint8_t*)0x50000000;	/* not allowed */
*pData = 50;					/* allowed */
```

Here, the pointer `pData` is not modifiable (read-only), but **from `pData`'s perspective** the memory location it points to is modifiable.

**Use case:**

Use to improve readability and guard the pointer variables.

```c
/* updates the details of age and salary into the pointer provided by the caller */
void update_user_data(uint8_t *const pUserAge, uint32_t *const pUserSalary)
{
    if (pUserAge != NULL)
        *pUserAge = getUserAge();
    
    if (pUserSalary != NULL)
        *pUserSalary = getUserSalary();
}
```

### Case 4: Constant pointer to constant data

**Syntax:**

```c
const uint8_t *const pData = (uint8_t*)0x40000000; /* pData is a constant pointer to a constant data */

pData = (uint8_t*)0x50000000;	/* not allowed */
*pData = 50;					/* not allowed */
```

Here, the pointer `pData` is not modifiable (read-only), and **from `pData`'s perspective** the memory location it points to is also not modifiable (read-only).

**Use case:**

Use when reading data from the status register of the hardware. Contents of the status register shouldn't be modified by the programmer.

```c
/* reads and returns the content of the status register pointed to by pStatusReg 
   (accidental write to the status register may cause unpredictable consequences) */
uint32_t read_status_register(const uint32_t *const pStatusReg)
{
    return (*pStatusReg);
}
```



## More Examples

The first `const` keyword can be on either side of the type:

```c
const int *ptr;			/* int const *ptr; */
const int *const ptr;	/* int const * const ptr; */
```

More examples on `const` usage:

```c
int **ptr;					/* pointer to a pointer to an int */
int ** const ptr;			/* const pointer to a pointer to an int */
int * const *ptr;			/* pointer to a const pointer to an int */
const int **ptr;			/* pointer to a pointer to a const int */
int * const * const ptr;	/* const pointer to a const pointer to an int */
```
