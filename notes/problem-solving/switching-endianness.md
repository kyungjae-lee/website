[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > Switching Endianness

# Switching Endianness

In embedded systems and low-level firmware development, handling endianness correctly is essential, especially when dealing with communication protocols, binary file formats, or cross-platform data exchange. Write a macro to switch the endianness of a 32-bit value.



## A Naive Macro Implementation

```c
#define SWITCH_ENDIANNESS(X)	\
	((X & 0xFF) << 24 |			\
	 (X & 0xFF00) << 8 |		\
	 (X & 0xFF0000) >> 8 |		\
	 (X & 0xFF000000) >> 24)
```

At first glance, this macro appears correct. It reverses the byte order of a 32-bit value. However, it has several issues that are worth addressing.

### Problems with the Naive Approach

##### 1. Multiple Evaluation of the Argument

Macros do not protect against multiple evaluations of their arguments.

```c
SWITCH_ENDIANNESS(i++)
```

This results in undefined behavior and subtle bugs. In safety-critical or embedded systems, this is unacceptable.

##### 2. Missing Type Safety

The macro does not specify the expected width or signedness of `X`. Depending on the platform, `int` may not be 32 bits, and signed shifts may cause issues.

##### 3. Reduced Readability

A one-line bitwise expression is harder to read, review, and maintain, especially in large firmware projects.

### Improved Solution

```c
#define SWITCH_ENDIANNESS_U32(X)			\
	( (((uint32_t)(X) & 0x000000FFU) << 24)	\
      (((uint32_t)(X) & 0x0000FF00U) << 8)	\
      (((uint32_t)(X) & 0x00FF0000U) << 8)	\
      (((uint32_t)(X) & 0xFF000000U) << 24) )
```





## A Safer and More Readable Solution

In real-world embedded code, a `static inline` function is usually preferred over a macro as it offers safety without sacrificing performance.

```c
#include <stdint.h>

static inline uint32_t switch_endianness_u32(uint32_t x)
{
    return ((x & 0x000000FF) << 24) | /* byte 0 -> byte 3 */
           ((x & 0x0000FF00) << 8)	| /* byte 1 -> byte 2 */
           ((x & 0x00FF0000) >> 8)	| /* byte 2 -> byte 1 */
           ((x & 0xFF000000) >> 24)	| /* byte 3 -> byte 0 */
}
```

### Why This is Better

* The argument is evaluated exactly once.
* The data width is explicit (`uint32_t`).
* Bit masks and shifts are clearer and easier to audit.
* The compiler can still inline this function with zero overhead.

