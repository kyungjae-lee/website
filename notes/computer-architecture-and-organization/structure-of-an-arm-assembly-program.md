[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Computer Architecture & Organization</a> > Structure of an ARM Assembly Program

# Structure of an ARM Assembly Program



## Structure of an ARM Assembly Program (for the Raspberry Pi)

* There are differences between the textbook and the Raspberry Pi assembler directives. (We will be using the ones for the Raspberry Pi.)

* Structure of an ARM Program for the Raspberry Pi: (Try to follow this format.)

  ```plain
  @ Structure of an ARM Program for the Raspberry Pi 
  
  @ --------------------------------------------------------------------------------------
  .text         @ Defines a read-only section. (Code starts here)
                @ Everything from here to .data (read/write) section, you don't
                @ want the code to be modified or overwritten by accident.
                @ Attempt to write in this section will produce 'seg fault'.
                @ .text tells the assembler to switch to the text segment
                @ (where code goes).
                
  @ --------------------------------------------------------------------------------------
  .global main  @ Defines the location of the first instruction to be executed.
                @ This is the memory address that will be loaded into the PC.
  main:         
  
                @ Make sure to have 'exit:' statement in this area so your program
                @ terminates at some point.
  
  @ --------------------------------------------------------------------------------------
  .data         @ Defines a read/write section.
                @ .data tells the assembler to switch to the data segment
                @ (where data goes).
  
  .balign 4     @ Forces a word boundary.
                @ 4 specifies the number of bytes that must be aligned to.
                @ (this number must be power of 2).
                @ It means, the next piece of memory that I declare after this directive
                @ need to start at a memory location that is divisible by 4. It has to be
                @ aligned with the memory locations that are divisible by 4.
  
  Q:  .word 9   @ Defines a label Q at current memory location as word-size and
                @ sets it to a decimal 9.
                @ .word  allocates 4 byte memory space to hold data.
                @ .hword allocates 2 byte memory space to hold data.
                @ .byte  allocates 1 byte memory space to hold data.
  
  Str: .asciz "This is a sample string.\n"
                @ '.asciz' adds terminating null character at the end so you don't have to
                @ include it manually.
                @
                @ `.ascii` does NOT add terminating null character so you have to
                @ explicitly add it at the end of the string.
                @ (e.g., .ascii "This is a sample string.\0\n")
                @ ∴ Just use '.asciz' and make your life eaiser.
  
  Array: .skip 4x10 @ Defines a label 'Array' at current memory location and reserves
                    @ 40 bytes of memory.
                    @ Note that '.skip' does NOT initialize the allocated memory; Thus
                    @ 'Array' will contain whatever garbage values it was previously 
                    @ set to.
  
  @ --------------------------------------------------------------------------------------
  .global XXX   @ Defines any external calls like printf, scanf, etc.
  
  @ --------------------------------------------------------------------------------------
  .end          @ Defines the end of the assembly code. 
                @ There is no more assembly code after this point in the file.
                @ Can be replaced by a blank line.
  @ --------------------------------------------------------------------------------------
  ```

  > Labels such as `main:`, `.end`, `Str` represent addresses! Be aware how these are called in the main section.



## ARM Assembly Program Examples

### Example 1

* **C Code** 

  ```c
  X = P - Q;
  
  if (X >= 0) { X = P + 5; }
  else { X = P + 20; }
  ```

* **ARM Assembly Code (for the Raspberry Pi)**    

  ```assembly
  @ ---------------------------------------------------------------------------
  @ Case 1: P = 12, Q = 9, results in X = 17 (executes 'if' part)
  @ ---------------------------------------------------------------------------
  
  .text
  
  .balign 4             @ Make sure the code is on full word boundaries.
  
  .global main          @ Entry point for the program. (Must be global)
  
  main:
    ldr  r0, =P         @ Load the address of P
    ldr  r0, [r0]       @ Load the value of P
    ldr  r1, =Q         @ Load the address of Q
    ldr  r1, [r1]       @ Load the value of Q
    ldr  r3, =X         @ Load the address of X
    subs r2, r0, r1     @ Do the subtraction and set the CCR flags
    bpl  then           @ If positive, add 5
    add  r2, r0, #20    @ Else (if negative), add 20
    b    exit           @ Done with calculation
  
  then:
    add  r2, r0, #5
  
  exit:
    str  r2, [r3]       @ Write the result back to X in memory
    mov  r0, r2         @ Put x in r0 so it can be echo printed
    bx   lr             @ Stop
  
  .data
  
  .balign 4             @ Force to the next whole word.
  X:  .word 0           @ Define one word for X and initialize to 0
  
  .balign 4             @ Force to the next whole word.
  P:  .word 12          @ Define one word for P and initialize to 12
  
  .balign 4             @ Force to the next whole word.
  Q:  .word 9           @ Define one word for Q and initialize to 9
  
  
  @ ---------------------------------------------------------------------------
  @ Case 2: P = 12, Q = 14, results in X = 32 (executes 'else' part)
  @         (Same logic with changed data value, Q = 14)
  @ ---------------------------------------------------------------------------
  
  .text
  
  .balign 4             @ Make sure the code is on full word boundaries.
  
  .global main          @ Entry point for the program. (Must be global)
  
  main:
  ldr  r0, =P         @ Load the address of P
  ldr  r0, [r0]       @ Load the value of P
  ldr  r1, =Q         @ Load the address of Q
  ldr  r1, [r1]       @ Load the value of Q
  ldr  r3, =X         @ Load the address of X
  subs r2, r0, r1     @ Do the subtraction and set the CCR flags
  bpl  then           @ If positive, add 5
  add  r2, r0, #20    @ Else (if negative), add 20
  b    exit           @ Done with calculation
  
  then:
  add  r2, r0, #5
  
  exit:
  str  r2, [r3]       @ Write the result back to X in memory
  mov  r0, r2         @ Put x in r0 so it can be echo printed
  bx   lr             @ Stop
  
  .data
  
  .balign 4             @ Force to the next whole word.
  X:  .word 0           @ Define one word for X and initialize to 0
  
  .balign 4             @ Force to the next whole word.
  P:  .word 12          @ Define one word for P and initialize to 12
  
  .balign 4             @ Force to the next whole word.
  Q:  .word 14          @ Define one word for Q and initialize to 14
  ```

### Example 2

* **C Code**

  ```c
  /* Calculates 1 + 2 + 3 + ... + 19 + 20 */
  
  int counter = 1;
  int rsum = 0;
  
  while (counter < 21)
  {
      rsum += counter;
  }
  ```

* **ARM Assembly Code (for the Raspberry Pi)**

  ```assembly
  @ ---------------------------------------------------------------------------
  @ Calculates 1 + 2 + 3 + ... + 19 + 20
  @ ---------------------------------------------------------------------------
  
  .text
  
  .balign 4             @ Make sure the code is on full word boundaries.
  
  .global main          @ Entry point for the program. (Must be global)
  
  main:
    mov  r0, #1         @ Counter set to 1
    mov  r1, #0         @ Running sum set to 0
  
  next:
    add  r1, r1, r0     @ Add the counter to the running sum
    add  r1, r0, #1     @ Increment the counter
    cmp  r0, #21        @ If counter < 21
    bne  next           @ Branch to do the next loop
    mov  r0, r1         @ Else, put sum into r0 so it can be displayed
    bx   lr             @ Stop
  
  .data
  ```
