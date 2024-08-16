[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Computer Architecture & Organization</a> > The Stored Program Concept

# The Stored Program Concept



## Fundamental Action of a Stored Program Machine

* Program execution cycle expressed in pseudocode

  ```plain
  /* Stored program machine */
  
      Point to the first instruction in memory
  
      REPEAT
          Read the instruction at the memory location pointed at
          Point to the next instruction
          Decode the instruction read from memory
          Execute the instruction
      FOREVER
  End
  ```

* `Execute the instruction` step in detail (in pseudocode)

  ```plain
  /* Execute the instruction */
  
      IF the instruction requires data
          THEN fetch the data from memory
      END_IF
      Perform the operation defined by the instruction
      IF the instruction requires data to be stored in memory
          THEN store the data in memory
      END_IF
  End
  ```

* Program execution cycle expressed in C

  ```plain
  ProgramCounter = 0;                         // point to the 1st instruction
  
  do
  {
      instruction = memory[ProgramCounter];   // read the instruction
      updatepc(ProgramCounter);               // pc point to next instruction
      decode(instruction);                    // decode the instruction
      fetch(operands);                        // fetch required data
      execute;                                // execute the instruction
      store(results);                         // store the result
  } while (instruction != terminate);
  ```

* Stored program machines require *two* memory access per instruction.

  - One to read the instruction from memory.
  - The other to either read data from memory that is required by the current instruction or to put data in memory that previously has been created or modified in the CPU by an earlier instruction.
  - The term **von Neumann bottleneck** indicates that one of the problems of the stored program computer is the path between the CPU and the memory.
      - [Harvard Architecture](introduction-to-computer-system-architecture) solves this issue by using separate storages and buses for *data* and *instructions*.

* Store program machines are said to operate in a two-phase mode called a *fetch/execute* cycle.




## Three Address (Operand) Instructions

* Format 

  ```plain
  Operation Address1, Address2, Address3
  (Operation Destination, Source1, Source2)
  ```

  > `Operation` specifies the action the instruction is to carry out (e.g., add, subtrace, multiply, etc.) and `Address1`, `Address2`, `Address3` are the locations of the three operands in memory. 
  >
  > There is a standard for the way in which the sequence of operands is written. Different computers may use the different notation.

* Equivalent RTL Notation

  ```plain
  [Address1] ← [Address2] Operation [Address3]
  ([Destination] ← [Source1] Operation [Source2])
  ```

* Example

  ```plain
  ADD P, Q, R
  [P] ← [Q] + [R] // in RTL notation
  ```

  > `ADD P, Q, R` is the symbolic form of the actual instruction that would be stored in memory as a binary sequence of 1s and 0s, meaning "add the contents of `Q` to the contents of `R` and put the result in `P`".
  >
  > `P`, `Q`, and `R` are the symbolic names of the addresses of three memory locations.
  >
  > This operation requires *four* memory accesses (e.g., one to fetch the instruction, two to fetch the two source operands, and one to store the result).
  >
  > Here `ADD` is called an **op-code** (abbreviated from **operation code** ). An op-code is the portion of a machine language instruction that specifies the operation to be performed.

  


## Two Address (Operand) Instructions

* Format 

  ```plain
  Operation Address1, Address2
  (Operation Destination, Source1)
  ```

  > `Address2` is a source operand and `Address1` is *both* a source and a destination operand.
  >
  > The source operand `Address1` is replaced (overwritten) by the result.
  >
  > Practical computers do not generally allow you to use *two* memory addresses in the same instruction. Most computers specify one address in memory and a second address as a register.

* Equivalent RTL Notation

  ```plain
  [Address1] ← [Address1] Operation [Address2]
  ([Destination] ← [Destination] Operation [Source])
  ```

* Example

  ```plain
  ADD P, Q
  [P] ← [P] + [Q] // in RTL notation
  ```



## One Address (Operand) Instructions

* **Format**

  ```plain
  Operation address
  ```

* **Example**

  ```plain
  LOAD    Q   (Read Q into the accumulator)
  ADD     R   (Add R to the accumulator)
  STORE   P   (Store the accumulator in P)
  ```

  > Above instructions simply mean `P = Q + R`. 
  >
  > Because only one address is provided and at least two addresses are required, the processor has to use an operand that doesn't require an explicit address (`Q` in this example). That is, this operand comes from a *register* once called the *accumulator* within the CPU.
  >
  > In some cases, accumulator acts as a bottleneck, because all of the data has to flow through it.




## Addressing Mode

* **Addressing modes** are an aspect of the instruction set architecture in most CPU designs.
* The various addressing modes that are defined in a given instruction set architecture define how the machine language instructions in that architecture identify the operand(s) of each instruction.
* An addressing mode specifies how to calculate the effective memory address of an operand by using information held in registers and/or constants contained within a machine instruction or elsewhere.
* Examples
  - Location of an operand given *directly* (i.e., it's in location `1000`).
  - Location of an operand given *indirectly* (i.e., `register 6` contains the address of the operand we need).




## Computer Categories

Computers can be classified by the way in which their instructions act on data.

* **Memory-to-memory**
  - Instructions can read source operands from memory, perform an operation on the data, and store (return) the result in memory.
  - These types of computers (CPUs) do not even require the use of registers to execute instructions.
* **Register-to-register**
  - Computers that perform operations only on the contents of registers.
  - Also called, *load/store computers* since `LOAD` and `STORE` operations are the only memory access instructions provided.
      - `LOAD` instruction must be used to put the data in registers.
      - `STORE` instruction must be used to transfer data from a register to memory.
  - Falls into the class of *RISC*
  - e.g., ARM, MIPS
* **Register-to-memory**
  - Computers that can perform operations on two operands where one is in memory and the other in a register (the result is written to either memory or a register).
  - Falls into the class of *CISC*
  - e.g., Intel IA32 (8086, Pentium, Core i7)
