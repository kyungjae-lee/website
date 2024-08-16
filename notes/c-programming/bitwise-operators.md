[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C Programming</a> > Bitwise Operators

# Bitwise Operators



## 6 Commonly Used Bitwise Operators in Embedded C Programming

### Bitwise Logic Operators

* `&` (Bitwise AND)
* `|` (Bitwise OR)
* `~` (Bitwise NOT - Negation) Unary
* `^` (Bitwise XOR) - Bitwise addition without carry

### Bitwise Shift Operators

* `>>` (Bitwise Right Shift)
* `<<` (Bitwise Left Shift)



## Logical Operator vs. Bitwise Operator

* `&&` is a logical AND operator
* `&` is a bitwise AND operator

Example:

```c
char A = 40;
char B = 30;
char C;

// Logical AND vs. bitwise AND
C = A && B;	// C contains 1 (meaning true)
C = A & B;	// C contains '00001000(2)' which is 8
                // A 00101000
                // B 00011110
                // -----------
                // C 00001000
                
// Logical OR vs. Bitwise OR
C = A || B;	// C contains 1 (meaning true)
C = A | B;	// C contains '00111110(2)' which is 62
                // A 00101000
                // B 00011110
                // -----------
                // C 00111110
                
// Logical NOT vs. Bitwise NOT
C = !A;		// C contains '0' (meaning false)
C = ~A;		// C contains '11010111(2)' which is -41

// Bitwise XOR
C = A ^ B	// C contains '00110110(2)'
                // A 00101000
                // B 00011110
                // -----------
                // C 00110110
```



## Bitwise Logic Operators

In embedded C programs, most of the time you will be doing:

* Testing of bits (`&`)
  * Testing bits to analyze the status register of a peripheral
* Setting of bits (`|`)
  * e.g., Setting bits to turn on LED
* Clearing of bits (`~` and `&`)
  * e.g., Clearing bits to turn off LED
* Toggling of bits (`^`)

### Exercise

Write a program which takes 2 integers from the user, computes bitwise `&`, `|`,`^` and `~` and prints the result.

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int n1, n2;
    
    printf("Enter two integers: ");
    scanf("%d %d", &n1, &n2);
    
    printf("%d & %d = %d\n", n1, n2, n1 & n2);
    printf("%d | %d = %d\n", n1, n2, n1 | n2);
    printf("%d ^ %d = %d\n", n1, n2, n1 ^ n2);
    printf("~%d = %d, ~%d = %d\n", n1, ~n1, n2, ~n2);
    
    return 0;
}
```

```plain
Enter two integers: 1 2
1 & 2 = 0
1 | 2 = 3
1 ^ 2 = 3
~1 = -2, ~2 = -3
```

### Use Case: Testing of Bits

Write a program to find out whether a use entered integer is even or odd. Print an appropriate message on the console. Use testing of bits logic.

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int n;
    
    printf("Enter an integer: ");
    scanf("%d", &n);
    
    if (n & 1)	// here 1 is the mask value
        printf("%d is an odd number.\n", n);
    else
        printf("%d is an even number.\n", n);
    
    return 0;
}
```

```plain
Enter an integer: 42
42 is an odd number.
```

Bit masking is a technique in programming used to test or modify the states of the bits of a given data.

* Modify : If the state of the bit is zero make it one, or if the state of the bit is 1 then make it 0.

* Test: Check whether the required bit position of a data is 0 or 1.

### Use Case: Setting of Bits

Write a program to set(make bit state to 1) 4th and 7th bit position of a given number and print the result.

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int n = 0x0;
    
    n |= (1 << 4);		// set 4th bit position to 1
    n |= (1 << 7);		// set 7th bit position to 1
    printf("%d\n", n);	// 10010000(2) or 144
    
    return 0;
}
```

```plain
144 
```

Could've done `n |= 0x90;` instead.

### Use Case: Clearing of Bits

Write a program to clear(make bit state to 0) 4th, 5th, 6th bit positions of a given number and print the result.

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int n = 0xFF;
    
    n &= ~(1 << 4);		// clear 4th bit position
    n &= ~(1 << 5);		// clear 5th bit position
    n &= ~(1 << 6);		// clear 6th bit position
    printf("%d\n", n);	// 10001111(2) or 143
    
    return 0;
}
```

```plain
143
```

Alternative 2 ways:

```c
 n &= 0x8F;			// using 10001111(2)
```

> Directly perform bitwise AND
>
> You need to think and calculate the mask value.

```c
 n &= ~(7 << 4);	// using 01110000(2)
```

> Negate the mask value first and then perform bitwise AND
>
> This method could be a shortcut since it it easier to identify the bit pattern to clear and shift it to the proper position. In this case, 111(2) is 7 and we just need to shift 7 to the right 4 places.

### Use Case: Toggling of Bits

Rewrite the following code snippet using toggling of bits.

```c
while (1)
{
    if (LED == 0)
        LED == 1;
    else
        LED == 0;
}
```

Using bitwise XOR operator `^`, above code can be rewritten as:

```c
while (1)
    LED ^= 0x01;
```



## Bitwise Shift Operators

* Bits of the 1^st^ operand will be right (`>>`) or left (`<<`) shifted by the amount decided by the 2^nd^ operand.

  ```c
  /* syntax */
  operand1 >> operand2 	/* right shift */
  operand1 << operand2 	/* left shift */
  ```

  > A value will be **multiplied** by 2 for each **left shift**.
  >
  > A value will be **divided** by 2 for each **right shift**.

* In an embedded C programs, bitwise shift operators are frequently used to bit-mask data along with other bitwise logic operators. Predominantly used while **setting** or **clearing** bits. (See *Use Case: Setting of Bits*, and *Use Case: Clearing of Bits* section above)

### Use Case: Bit Extraction 

* Extract bit positions from 9^th^ to 14^th^ [14:9] in a given data and save it in to another variable:

  1. Shift the identified portion to right hand side until it touches the least significant bit (0th bit).
  2. Mask the value to extract only 6 bits [5:0] and then save it in to another variable.

  ```c
  uint16_t data = 0xB410;	/* given data */
  uint8_t output = (uint8_t)((data >> 9) & 0x003F);
  ```





## References

Nayak, K. (2022). *Microcontroller Embedded C Programming: Absolute Beginners* [Video file]. Retrieved from  https://www.udemy.com/course/microcontroller-embedded-c-programming/
