[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Computer Architecture & Organization</a> > ARM's Load & Store Encoding

# ARM's Load & Store Encoding



## ARM's Load & Store Encoding

* The `LDR` and `STR` can have conditional execution.

  ```plain
  @ if (a == b) { x = p; } else { x = q; }
  
  CMP   r1, r2
  LDREQ r3, [r4]
  LDRNE r3, [r5]
  ```

* Not sure if the addition or subtraction of an offset is part of these two instructions only or it is just discussed only here in the text.

  ```plain
  LDR r0, [r1, +r2]
  LDR r0, [r1, -r2]     @ Pre-indexed; the base register r1 is NOT updated.
  ```

* If you need to access a byte in memory, the `LDR` and `STR` (and only these two) support that with adding a "`B`" to the instruction.

  Default is to access 32-bit word.

  ```plain
  STRB r0, [r2]
  LDRB r1, [r1]
  ```

  This is necessary if you need to parse a string to look at each byte (character) at a time, or limit the user input using `%s` versus `%c`.
