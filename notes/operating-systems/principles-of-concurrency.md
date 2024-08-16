[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Principles of Concurrency

# Principles of Concurrency



## Introduction

* Operating systems are concerned with the management of processes and threads.

  * **Multiprogramming** - the management of multiple processes within a <u>uniprocessor</u> system

    - **Logical concurrency** - the execution of several programs takes place in an interleaving fashion on a single processor

  * **Multiprocessing** - the management of multiple processes within a <u>multiprocessor</u> system

    - **Physical concurrency** - several program units (or execution flows) from the same program literally execute simultaneously (or in parallel) on more than one processor 

      Physical concurrency is related to **parallelism**.

    - **Logical concurrency** still applies to the way each processor executes processes

  * **Distributed processing** - the management of multiple processes executing on multiple, distributed computer systems (e.g., clusters)

  Concurrency is fundamental to all of these types of computing.

* Concurrency arises in different contexts:
  * **Multiple applications** - multiprogramming was invented to handle this, and allow processing time to be shared among multiple active applications
  * **Structured applications** - modular design sometimes encompasses developing problem solutions built from multiple concurrent processes
  * **Operating system structure** - OS services can be developed as a set of concurrent processes as well
  
* An integral requirement to programming with concurrency is the ability to enforce **mutual exclusion**.

* When working with the language-level features of concurrency, check if they support physical concurrency or logical concurrency or both. For example:

  In Java, JVM emulates only one processor. So, even if your Java program is deploying multiple threads, they will eventually run on uniprocessor (multiprogramming) environment. Thus, logical concurrency only!

  In Rust, the threads are physically concurrent. So, if you have multiple processor, they can physically run on multiple processors at the same time.



## Key Terms Related to Concurrency

* **Atomic operation**

  A function or action implemented as a sequence of one or more instructions that appears to be indivisible; that is, no other process can see an intermediate state or interrupt the operation. The sequence of instruction is guaranteed to execute as a group, or not execute at all, having no visible effect on system state. Atomicity guarantees isolation from concurrent processes.

* **Critical section**

  A section of code in a process that requires access to shared resources and must not be executed when another process is in a corresponding section of code. (In general, more than one process accessing the shared resource is considered as "**Perilous behavior**");

* **Deadlock**

  A situation in which two or more processes are unable to proceed because each is waiting for the other to finish (i.e., circular wait).

* **Livelock**

  A situation in which two or more processes continuously change their states in response to the other process(es) without doing any useful work.

  Livelock is like deadlock in the sense that two (or more) processes are blocking each other but with livelock, each process is actively trying to resolve the problem on its own (like reverting back and retry). A livelock happens when the combination of these processes efforts to resolve the problem make it impossible for them to ever terminate.

  e.g., A husband and wife are trying to eat soup, but only have one spoon between them. Each spouse is too polite, and will pass the spoon if the other has not yet eaten.

  e.g., Two people attempting to pass each other in a corridor: Person A moves to his left to let person B pass, while person B moves to his right to let person A pass. Seeing that they are still blocking each other, person A moves to his right, while person B moves to his left. They're still blocking each other.

* **Mutual exclusion**

  The requirement that when one process is in a critical section that accesses shared resources, no other process may be in a critical section that accesses any of those shared resources.

* **Race condition**

  A situation in which multiple threads or processes read and write a shared data item, and the final result depends on the relative timing of their execution. Race condition is caused by the **non-determinism** (i.e., in a multi-threaded concurrent environment, the order in which each instruction will be interleaved is non-deterministic). For example,

  ```c
  int count = 10;	// shared variable
  ```

  ```c
  // producer thread 
  count++;
  
  // internal operation
  // 1. register1 = count (LOAD)
  // 2. register1 = register1 + 1	(Modify - ADD)
  // 3. count = register1 (STORE)
  ```

  ```c
  // consumer thread
  count--;
  
  // internal operation
  // 1. register2 = count (LOAD)
  // 2. register2 = register2 - 1 (Modify - SUB)
  // 3. count = register2 (STORE)
  ```

  > Depending on how the instructions of producer and consumer gets interleaved, the resultant value of `count` may be 9, 10, or 11.

* **Starvation**

  A situation in which a runnable process is overlooked indefinitely by the scheduler; although it is able to proceed, it is never chosen.



## Process Interaction

* We can classify the ways in which processes interact on the basis of the degree to which they are aware of each other's existence.

  | Degree of Awareness                                          | Relationship                                                 | Influence that One Process Has on the Other                  | Potential Control Problems                                   |
  | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | Processes unaware of each other (there may or may not be shared resources) | Competition for resources<br><br/>Since the processes are unaware of each other no SW measure will be applied to mitigate the competition | Results of one process independent of the action of others<br><br>Timing of process may be affected | Mutual exclusion<br><br>Deadlock (renewable resource)<br><br>Starvation (on OS's hand) |
  | Processes indirectly aware of each other (e.g., shared object) | Cooperation by sharing                                       | Results of one process may depend on information obtained from others<br><br>Timing of process may be affected | Mutual exclusion<br/><br/>Deadlock (renewable resource)<br/><br/>Data coherence |
  | Processes directly aware of each other (have communication primitives available to them) | Cooperation by communication                                 | Results of one process may depend on information obtained from others<br/><br/>Timing of process may be affected | Deadlock (renewable resource)<br/><br/>Starvation            |
  
  > Processes unaware or indirectly aware of each other $\to$ Relies much on what the OS can provide.
  >
  > Processes directly aware of each other $\to$ More on the programmer's hand to make the decisions. (It is the programmer's responsibility to manage and use locks in such a way that no potential control problems will occur.)



## Requirements for Mutual Exclusion

1. Mutual exclusion must be enforced - only one process at a time is allowed into its critical section, among all processes that have critical sections for the same resource or shared object.

2. A process that halts in its noncritical section must do so without interfering with other processes.

3. It must not be possible for a process requiring access to a critical section to be delayed indefinitely - no deadlock or starvation.

4. A process must not be denied or delayed access to a critical section when no other processes are using.

5. No assumptions are made about relative process speeds or number of processors.

6. A process remains inside its critical section for a finite amount of time only.

   ```c
   // example of a BAD programming practice
   lock(m);
   while (1) { ... }	// holding lock forever
   unlock(m);
   ```



## How does the OS Enforce Mutual Exclusion?

1. **Disabling interrupts (hardware way)**

   Lowest-level method of enforcing the mutual exclusion

2. **Atomic operations (hardware primitive constructs)**

   - Test-and-set instruction
   - Compare-and-swap instruction

3. **Semaphores (objects provided by the OS for the programmers to use)**

   Higher-level than the hardware atomic operations

   - Binary semaphores
   - Counting semaphores

4. **Monitors**

   Even higher-level object than the semaphores.

   Programming languages build monitors using the semaphores and locks that operating systems provide. (c.f., All objects in Java are very primitive monitors)

5. **Message passing**

   Highest-level method of enforcing the mutual exclusion.

   A process has to wait (in blocked state) until it receives the message, etc.

See [Mutual Exclusion - Software Emulation](./mutual-exclusion-software-emulation) for software emulation of locks, which does not involve the OS.






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
