[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Computer Architecture & Organization</a> > ARM Instruction Set Architecture (ISA) - Overview

# ARM Instruction Set Architecture (ISA) - Overview



## General Classification of Instructions

* Data Movement
* Arithmetic
* Logical
* Shift
* Program Control



## Updating ARM Condition Codes

* ARM will only update the condition codes when specifically called for by the instruction.    

  ```plain
  ADDS r1, r2, r3       @ 'S' calls for an update in the condition codes
  ADD  r1, r2, r3       @ Will not update the condition codes
  ```



## ARM Data Processing, Data Transfer, and Compare Instructions

- Remember! This only tells a part of the story. For more information, see [ARM Assembly Language Reference Card](arm-assembly-language-reference-card).

  ```plain
  Instruction               ARM Mnemonic        Definition (RTL)
  ========================  ==================  ============================
  Addition                  ADD r0, r1, r2      [r0] ← [r1] + [r2]
  Subtraction               SUB r0, r1, r2      [r0] ← [r1] - [r2]
  AND                       AND r0, r1, r2      [r0] ← [r1] · [r2]
  OR                        ORR r0, r1, r2      [r0] ← [r1] + [r2]
  Exclusive OR              EOR r0, r1, r2      [r0] ← [r1] ⊕ [r2]
  Multiply                  MUL r0, r1, r2      [r0] ← [r1] x [r2]
  Register-register move    MOV r0, r1          [r0] ← [r1]
  Compare                   CMP r1, r2          [r1] - [r2]
  Branch on zero to label   BEQ label           [PC] ← label (jump to label)
  ```
