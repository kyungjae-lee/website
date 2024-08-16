[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Mutual Exclusion - Monitors (Programming Language Support)

# Mutual Exclusion - Monitors (Programming Language Support)



## Problems with Semaphores

* Semaphores are primitive yet powerful and flexible tool for enforcing mutual exclusion and for coordinating processes. However, it may be difficult to produce a correct program using semaphores. i.e., `sem_wait()` and `sem_signal()` may be scattered throughout a program, and it is not easy to see the overall effect of these operations on the semaphores they affect.

  

## Monitors

*  The **monitor** is a programming language construct that provides equivalent functionality to semaphores, but is easier to control and verify.
* A monitor is a software module consisting one or more procedures, an initialization sequence and local data.
* The local data variables are accessible only by the monitor's procedures and not any external procedure (as private attributes in a class object).
* A process enters the monitor by invoking one of its procedures.
* In basic monitors, only one process may be executing in the monitor at a time, the others block to wait (i.e., mutual exclusion).
* Still possible to cause deadlock if programmed incrrectly.

### Condition Variables

* Monitors use condition variables to manage synchronization.

* Condition variables are internal to the monitor (i.e., accessible only within the monitor), and therefore, cannot be scattered elsewhere in the code.

* Condition variables are a special data type in monitors, which are operated on by two functions:

  * `cwait(c)`

    Suspend execution of the calling process on condition `c`. The monitor is now available for use by another process.

  * `csignal(c)`

    Resume execution of some process blocked after a `cwait()` on the same condition. If there are several such processes, choose one of them; if there are none, do nothing.

  [!] Note: Note that `wait()` and `signal()` for condition variables are different from those of Semaphores'. In POSIX Threads library they are `pthread_cond_wait()` and `pthread_cond_signal()`, respectively. See [Thread Synchronization - Condition Variables](../multi-threading/thread-synchronization-condition-variables).

* Examples:

  Producer/consumer would use a monitor for the buffer data structure and the condition variables would be `isFull` and `isEmpty`. Still only one can be in the monitor at a time.

  

## The Object as Monitor

* All Java objects can behave as a monitor. They have a single built in condition variable.
* Use the synchronized keyword to indicate that Java threads using an object should wait for each other.
* Java also has other mechanisms for synchronizing threads, this is just one of them!






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
