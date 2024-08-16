[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Execution of the OS

# Execution of the OS



## Introduction

* The OS functions in the same way as ordinary computer software, in the sense that the OS is a set of programs executed by the processor (CPU).

* The OS frequently relinquishes control and depends on the processor to restore control back to the OS.

  How does the OS regain control of the CPU if needed? $\to$ At the end of the fetch-execute cycle, the hardware checks for interrupts. At this point an interrupt handler can load the next instruction in the OS that needs to take over. Because it happens at the end of every instruction, the OS won't get locked out forever.



## Typical Functions of an OS Kernel

* Process Management
  * Process creation and termination
  * Process scheduling and dispatching
  * Process switching
  * Process synchronization and support for interprocess communication (IPC)
  * Management of process control blocks (PCBs)
* Memory Management
  * Allocation of address space to processes
  * Swapping
  * Page and segment management
* I/O Management
  * Buffer management
  * Allocation of I/O channels and devices to processes
* Support Functions
  * Interrupt handling
  * Accounting
  * Monitoring



## OS Designs

### Non-process Kernel (Older OS Design)



<img src="./img/non-process-kernel.png" alt="non-process-kernel" width="550">



* Traditional approach, common on many older operating systems. (No longer used in modern operating systems.)

* In this model the concept of process is considered to apply only to user programs. The operating system code is executed as a separate entity that operates in privileged mode and has its own:

  * Reserved area of memory
  * System stack for controlling procedure calls and returns

* When a currently running process is interrupted or issues a supervisor call, that process' context is saved.

  $\to$ Control is transferred to the appropriate instruction within the kernel memory space.

  $\to$ OS kernel executes desired functionality

  $\to$ Then restores the context of the interrupted process which transfers control back to that process, or switches in a new one.

### OS Executes within User Processes



<img src="./img/os-within-user-processes.png" alt="non-process-kernel" width="550">



* In this model, almost all portion of the OS software is executed within the context of a user process, and switch execution modes (e.g., user mode $\leftrightarrow$ kernel/privileged mode). Only the core functionalities (i.e., process switching functions) are always resident in memory.

  This is the Linux's way of doing things. The OS creates a process that runs in user mode and it switches its mode to privileged mode by invoking system calls whenever necessary.

* Here, OS is primarily a collection of routines (or functions) that the user program calls.

* This requires an enhancement to the process image to provide extra storage for OS stack and sharable library code.

* Some of the modern operating systems use this model.

### Process-Based OS



<img src="./img/process-based-os.png" alt="process-based-os" width="550">



* Unlike the monolithic kernel, the operating system executes as a collection of system processes side-by-side with the user processes. Processes communicate with each other by using the defined methods (e.g., message passing).

* Encourages the use of a modular OS with minimal, clean interfaces between the modules.

* As a process, an OS module that takes care of non-critical operations can be assigned an appropriate priority level so that it can be interleaved with other processes under dispatcher control.

  Allows non-critical operations to run as lower priority processes, and allows critical operations to run on dedicated processor(s). Crucial part of the OS may run on a dedicated CPU and when it fails, it will move to another CPU so that the service does not stop. (Fault tolerance $\uparrow$)

* Implementing the OS as a set of processes is useful in a multiprocessor or multicomputer environment, in which some of the operating system services can be shipped out to dedicated processors, improving performance.

* Some of the modern operating systems use this model.



## OS Designs - Selected Example: UNIX SVR4

* Uses the model where most of the OS executes within the environment of a user process
* System processes run in kernel mode.
  * Executes operating system code to perform administrative and housekeeping functions
* User Processes
  * Operate in user mode to execute user programs and utilities
  * Operate in kernel mode to execute instructions that belong to the kernel
  * Enter kernel mode by issuing a system call, when an exception is generated, or when an interrupt occrus

* UNIX Process State Model (uses 9 states instead of 7)
  * **User running** - executing in user mode.
  * **Kernel running** - executing in kernel mode.
  * **Ready to run, in memory** - ready to run as soon as the kernel schedules it.
  * **Asleep in memory** - unable to execute until an event occurs; process is in main memory (a blocked state)
  * **Ready to run, swapped** - process is ready to run, but the swapper must swap the process into main memory before the knernel can schedule it to execute
  * **Asleep, swapped** - the process is awaiting an event and has been swapped to secondary storage (a blocked state)
  * **Preempted** - process is returning from kernel to user mode, but the kernel preempts it and does a process switch to schedule another process
  * **Created** - process is newly created and not yet ready to run
  * **Zombie** - process no longer exists, but it leaves a record for its parent process to collect



<img src="./img/unix-nine-state-model.png" alt="unix-nine-state-model" width="850">







## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
