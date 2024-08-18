[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Multi-Threading (POSIX Threads)</a> > Thread Synchronization - Spinlock

# Thread Synchronization - Spinlock



## Introduction to Spinlock

* When you stop at the traffic light in red, and it takes only 10 seconds to go green, you don't switch off your car engine, do you? Why so? The answer to this question forms the foundation of **spinlocks** (`pthread_spinlock_t`).
* A spinlock works basically the same way as a mutex does except the one difference:
  * When a thread is blocked by a **mutex**, the thread is taken off the CPU by the OS scheduler (i.e., forced context switch).
  * When a thread is blocked by a **spinlock**, the thread continues to spin in a loop ("spin") while repeatedly checking whether the lock is available. The thread will not release the CPU during that time. (i.e., The OS will never trigger context switching on that thread.)

* Spinlocks are preferred to mutexes when the critical section is very insignificant (i.e., involving only a small amount of operations). For example, when a critical section only includes setting/reading some flag(s).

  Using spinlocks to protect insignificant critical sections yields better performance as compared to using the mutexes. (Context switching, which is an expensive task, happens less with spinlocks.) And, this is why spinlocks were introduced in the first place.

* Obviously, over-use of spinlocks will waste CPU cycles.

* Use spinlocks just like using mutexes to safeguard critical sections.

* Spinlocks CANNOT be used along with condition variables.

* Spinlock APIs:

  * `pthread_spin_lock()`
  * `pthread_spin_unlock()`

  Read the spinlock section of the POSIX API for more information.

* Do NOT get confused with the kernel version of spinlocks! Here, we are talking about the user space spinlocks. The kernel uses different version of spinlocks which are used to synchronize the threads in the kernel space. (They are a bit more complicated.)






## References

Sagar, A. (2022). *Part A - Multithreading & Thread Synchronization - Pthreads* [Video file]. Retrieved from  https://www.udemy.com/course/multithreading_parta/

