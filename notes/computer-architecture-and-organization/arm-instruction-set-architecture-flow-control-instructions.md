[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Computer Architecture & Organization</a> > ARM Instruction Set Architecture (ISA) - Flow Control Instructions

# ARM Instruction Set Architecture (ISA) - Flow Control Instructions



## Unconditional Branch

* The *Program Counter (PC)* gets updated to the memory address pointed to by the **label**, unconditionally.

  ```plain
      B Label       @ Branch to 'Label'
  
  Label:
  ```

* This instruction is necessary because there is no structured programming when it comes to assembly.



## Conditional Branch

* Conditional branch is the real power of the modern computers. It alters the path of the code based on values.

* Because assembly does not support *if...else* statement, the programmer typically writes the program in pseudo-code in a structured manner, then translates it into the conditional branching that is supported.

  ```plain
  /* if x > y then x = x + 1 else y = y + 1 */
  
    cmp r1, r2        @ RTL: [r1] - [r2]  (r1 is x and r2 is y)
    bgt then          @ Branch to 'then' if r1 is greater than r2.
  
  else:
    add r2, r2 #1     @ else part
    b   exit          @ Branch to 'exit'. Don't forget this!
  
  then:
    add r1, r1 #1     @ then part
  
  exit:
    @ exit instruction
  ```

* Conditional branches test the bits in the CCR.

* **Condition codes** that can be attached to branch to form conditional branches:

  ```plain
  Encoding  Mnemonic  Branch on Flag Status             Execute on Condition
  ========  ========  ================================  =======================
  0000      EQ        Z set                             Equal (zero)
  0001      NE        Z clear                           Not Equal (not zero)
  0010      CS        C set                             Unsigned higher or same
  0011      CC        C clear                           Unsigned lower
  0100      MI        N set                             Negative
  0101      PL        N clear                           Positive or zero
  0110      VS        V set                             Overflow
  0111      VC        V clear                           No overflow
  1000      HI        C set and Z clear                 Unsigned higher
  1001      LS        C clear or Z set                  Unsigned lower or same
  1010      GE        N set and V set, or               Greater or equal
                        N clear and V clear                                 
  1011      LT        N set and V clear, or             Less than
                        N clear and v set                                   
  1100      GT        Z clear, and either N set and     Greater than
                        V set, or N clear and V clear
  1101      LE        Z set, or N set and V clear, or   Less than or equal
                        N clear and V set    
  1110      AL                                          Always (default)
  1111      NV                                          Never (reserved)
  ```

  Be aware that branches can take into account the **signed** or **unsigned** comparisons. Be careful when using the unsigned branches!!!

  ```plain
                      | A = 0110 1100 | B = 1110 1100
  --------------------+-------------------------------
  Unsigned comparison |             A < B     
  Signed   comparison |             A > B 
  ```

  > These condition codes can be used with non-branch instructions as well. 
  >
  > See [*ARM Assembly Language Reference Card*](arm-assembly-language-reference-card/).

## Compare and Test Instructions

* ARM has four compare and test instructions; **`TEQ`**, **`TST`**, **`CMP`**, **`CMN`**. 

  > All of these set the CCR flags by default, so an "`S`" does not have to be added to these instructions.   
  > Even if you do add it, assembler won't complain.

* **`TEQ` (Test Equivalence)**

  - Tests for equivalent or equal.

  - This is the same as the `EORS` instruction, except that the result is discarded.

  - Only `Z` bit will be changed based on the equivalency test. Other CCR flags will remain unchanged.

      - Set if equal
      - Clear if not equal

  - Example:

    ```plain
    TEQ r1, r2        @ If [r1] - [r2] is zero, sets Z flag
                      @ If [r1] - [r2] is NOT zero, clears Z flag
    ```

* **`TST` (Test Bits)**

  - Performs a bitwise `AND` on two values and sets ALL the CCR flags.

  - This is the same as the `ANDS` instruction, except that the result is discarded.

  - This is useful to test an individual bit in a register.

  - Example:

    ```plain
    TST r0, #2_00100000   @ Test if bit 5 of r0 is 1. (prefix '#_2' means 'binary')
    BEQ LowerCase         @ If bit 5 is 1 it is a lowercase letter.
    ```

* **`CMP` (Compare)**

  - Compares two values by subtracting one from another and sets all of the CCR flags.

  - Example:

    ```plain
    CMP r0, r1        @ Performs [r0] - [r1] and sets the CCR flags
                      @ accordingly.
    BEQ Equal         @ If [r0] - [r1] = 0, branch to Equal
    ```

* **`CMN` (Compare Negate)**

  - Negates the second operator then perform the compare. Sets ALL the bits of the CCR.

  - This is the same as the `ADDS` instruction, except that the result is discarded.

  - Example:

    ```plain
    CMN r0, r1        @ RTL: [r0] - (-[r1])
                      @ Essentially, [r0] + [r1].
    ```



## Branching and Loop Constructs

* No more new instructions here. We will see how the previously introduced instructions are used to implement different types of loop constructs.

### The FOR Loop

* Checks at the end of the construct.

  Be careful. Some languages will always execute the loop one time regardless of the value of the counter.

    - **C Code**

      ```plain
      for (i = 10; i > 0; i--)
      {
          /* code */
      }
      ```

    - **Assembly Code**

      ```plain
        MOV  r0, #10        @ Set up the loop counter
      
      ForLoop:
        /* code */          @ Body of the loop
      
        SUBS r0, r0, #1     @ Update counter at the bottom of the loop
        BNE  ForLoop        @ Continue until count zero
      ```

### The WHILE Loop

* Checks values at the top of the loop. If the condition is not met, the loop will not be entered.

    - **C Code**

      ```plain
      while (count > 0)
      {
          /* code */
          count--;
      }
      ```

    - **Assembly Code**

      ```plain
      WhileLoop:
        CMP  r0, #10        @ Test at the start of the loop
        BEQ  ExitWhileLoop  @ If [r0] = 10, break the loop
        /* code */          
        SUB  r0, r0, #1     @ Decrement the counter
        B    WhileLoop      @ Continue until count zero
      
      ExitWhileLoop:        @ Done with the while loop
      ```

### The UNTIL Loop or Do...While Loop

* Checks at the bottom of the loop. The loop will always execute at least one time.

    - **C Code**

      ```plain
      do
      {
          /* code */
      } while (expression)
      ```

    - **Assembly Code**

      ```plain
      UntilLoop:
        /* code */
        CMP  r0, #0         @ Test at the bottom of the loop
        BNE  UntilLoop
      ```

### Combination of Loops

* Sometimes the combination of the parts of all three loops produces effective code.

    - **For loop** limits the number of iteration.
    - **While loop** test keeps from entering the loop if not necessary.
    - **Until loop** allows for an exit condition.

* Regardless of the program language, it is always a good idea to have some code in place to ensure that you exit every loop.

* Example:

  ```plain
    MOV  r0, #10
  
  Loop:
    CMP  r1, #10        @ Test at the start of the while loop
    BEQ  ComboExit      @ Leave the loop
  
    /* code */
  
    CMP  r2, #0         @ Until loop test
    BEQ  ComboExit      @ Leave the loop
    SUBS r0, r0, #1     @ Decrement the for loop counter
    BNE  LoopStart     
  
  ComboExit:            @ Exit while loop
  ```




## Conditional Execution

* ARM allows each instruction to be **conditionally executed** based on the condition code.

  ```plain
  ADDEQ r1, r2, r3          @ RTL: If Z = 1, then [r1] ← [r2] + [r3]
  
  ADDCC r1, r2, r3, LSL r4  @ RTL: If C = 0, then [r1] ← [r2] + [r3] * 2^[r4]
  ```

* Conditional execution keeps us from from doing all the pesky branching for only one or two instructions that needs to be executed depending on the codes.

  With a pipeline system, branches slows the processor down because the pipe has to be flushed to keep unwanted instructions from being executed.

* For any ARM instruction, we can add the two letter condition codes to the instruction and make that instruction to be a conditional execution.

* Example:

  ```plain
  /* if ((a == b) && (c == d)) e++; */
  
  CMP   r0, r1      @ r0 is a and r1 is b
  CMPEQ r2, r3      @ r2 is c and r3 is d
  ADDEQ r4, r4, #1  @ r4 is e
  ```

  This does **short circuit**. If the result of the first comparison is NOT EQUAL, then the second and third comparisons are bypassed (*annulled* or *squashed*).

  Depending on the CPU, it may be faster to have several squashed instructions than to execute a branch statement.
