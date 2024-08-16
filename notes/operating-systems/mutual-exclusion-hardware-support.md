[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Mutual Exclusion - Hardware Support

# Mutual Exclusion - Hardware Support



## Interrupt Disabling

In a uniprocessor (multiprogramming) system, concurrent processes cannot have overlapped execution; they can only be interleaved. Furthermore, a process will continue to run until it invokes an OS service or until it is interrupted. Therefore, to guarantee mutual exclusion, it is sufficient to prevent a process from being interrupted. 

* This capability can be provided in the form of primitives defined by the OS kernel for disabling and enabling interrupts. A process can then enforce mutual exclusion in the following way:

  ```c
  while (true)
  {
      // disable interrupts
      // critical section
      // enable interrupts
      // remainder
  }
  ```

  > Since the critical section cannot be interrupted, mutual exclusion is guaranteed.

* Characteristics (drawbacks)

  * Degradation of execution efficiency due to the processor's limited ability to interleave processes.
  * Not applicable to the multiprocessor architecture (interrupt disabling does not guarantee mutual exclusion)



## Atomic Operations (Special Machine Instructions)

At the hardware level, access to a memory location excludes any other access to that same location. With this as a foundation, processor designers have proposed several machine instructions that carry out two actions **atomically**, such as reading/writing or reading/testing, of a single memory location within one instruction fetch cycle. During execution of the instruction, access to the memory location is blocked for any other instruction referencing that location.

### Test & Set Instruction

* Test and Set Lock (TSL) is a hardware-level synchronization mechanism that uses the atomic instruction `test_and_set`.

* `test_and_set` Instruction:

  * Returns the old value of a memory location `*word` and sets the memory location value to 1 as a single atomic operation.
  * If one process is currently executing a `test_and_set` instruction, no other process is allowed to begin another `test_and_set` until the first process `test_and_set` is finished.

* **Implementation**

  ```c
  // definition of test_and_lock
  boolean test_and_lock(boolean *word)
  {
      boolean oldval = *word;
      *word = true;
      return oldval;
  }
  ```

  ```c
  //  mutual exclusion using test_and_set
  int lock = 0;	// shared variable (initialized to 0) whose address is shared by multiple processors
  
  ...
      
  while (true)
  {
  	while (test_and_set(&lock));	// entry section
  	// critical section
  	lock = 0;					// exit section
  	// remainder section
  }
  ```

  > Initially, lock value is set to 0.
  >
  > `lock = 0` means the critical section is currently vacant and no process is present inside it.
  >
  > `lock = 1` means the critical section is currently occupied and a process is present inside it.
  >
  > The only process that may enter its critical section is one that finds `lock` equal to 0. All other processes attempting to enter their critical section go into a busy waiting mode. (**Busy waiting**, or **spin waiting**, refers to a technique in which a process can do nothing until it gets permission to enter its critical section, but continues to execute an instruction or set of instructions that tests the appropriate variable to gain entrance.)

* **Demonstration**

  * Scene 1:

    Process P0 arrives and executes the `test_and_set(lock)` instruction.

    Since the initial lock value is set to 0, `test_and_set` returns 0 and sets the lock value to 1. 

    Returned value 0 breaks the while loop.

    P0 enters the critical section and executes. 

    Now, even if P0 gets preempted in the middle, no other process can enter the critical section.

    Any other process can enter the critical section only after P0 has completed and sets the lock value back to 0.

  * Scene 2:

    Another process P1 arrives and executes the `test_and_set(lock)` instruction.

    Since the lock value is 1, `test_and_set` returns 1 and sets the lock value to 1. 

    Returned value 1 keeps P1 busy in the while loop until the lock value becomes 0.

  * Scene 3:

    P0 exits the critical section and sets the lock value to 0.

    P1's while loop breaks and P1 enters the critical section.

    Now, even if P1 gets preempted in the middle, no other process can enter the critical section.

    Any other process can enter the critical section only after P1 has completed and sets the lock value back to 0.

* **Characteristics**

  * Ensures mutual exclusion
  * Deadlock-free
  * Does not guarantee bounded waiting and may cause starvation
  * Suffers from spinlock
  * Not architectural neutral since it requires the operating system to support `test_and_set` instruction
  * A busy waiting solution which keeps the CPU busy even when the process is just waiting

### Compare & Swap Instruction

* Compare and Swap (CAS) is a hardware-level synchronization mechanism that uses the atomic instruction `compare_and_swap`.

* `compare_and_swap` instruction:

  * Returns the the old value of the memory location `*word`, replacing `*word`'s current value with `newval` only if the current value matches `testval`. This atomic operation has two parts; a **compare** and a **conditional swap**.
  * If one process is currently executing a `compare_and_swap` instruction, no other process is allowed to begin another `compare_and_swap` until the first process `compare_and_swap` is finished.

* **Implementation**

  ```c
  // definition of compare_and_swap
  int compare_and_swap(int *word, int testval, int newval)
  {
      int oldval;
      oldval = *word;
      if (oldval == testval) 
          *word = newval;
      return oldval;
  }
  ```

  ```c
  //  mutual exclusion using compare_and_swap
  const int n = /* number of process */;
  int lock = 0;	// shared variable (initialized to 0) whose address is shared by multiple processors
  
  ...
  
  void p(int i)
  {
      while (true)
      {
          while (compare_and_swap(&lock, 0, 1) == 1);	// do nothing
          // critical section
          lock = 0;
          // remainder
      }
  }
  
  void main()
  {
      lock = 0;
      parbegin(p(1), p(2), ... , p(n));
      // the construct 'parbegin(p1, p2, ... , Pn)' means, suspend the execution of the main program;
      // initiate concurrent execution of procedures P1, P2, ... , Pn;
      // when all of P1, P2, ... , Pn have terminated, resume the main program
  }
  ```

  > Initially, lock value is set to 0.
  >
  > `lock = 0` means the critical section is currently vacant and no process is present inside it.
  >
  > `lock = 1` means the critical section is currently occupied and a process is present inside it.
  >
  > The only process that may enter its critical section is one that finds `lock` equal to 0. All other processes attempting to enter their critical section go into a busy waiting mode. (**Busy waiting**, or **spin waiting**, refers to a technique in which a process can do nothing until it gets permission to enter its critical section, but continues to execute an instruction or set of instructions that tests the appropriate variable to gain entrance.)

  




## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
