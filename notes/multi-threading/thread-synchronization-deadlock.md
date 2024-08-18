[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Multi-Threading (POSIX Threads)</a> > Thread Synchronization - Deadlock

# Thread Synchronization - Deadlock



## Introduction to Deadlock



<img src="./img/deadlock.png" alt="deadlock" width="450">



* Deadlock is a situation in which two or more threads are unable to proceed because each is waiting for one of the others to do something. Threads may undergo deadlock if they are wrongly synchronized.

  

## 4 Necessary Conditions for Deadlocks

* The following 4 conditions must be met for a deadlock to be **possible**:

  1. **Mutual exclusion**

     One or more resource(s) are non-shareable (i.e., only one thread can use the resource at a time).

  2. **Hold and wait**

     A thread is holding at least one resource and waiting for other resources.

  3. **No preemption**

     A resource cannot be taken away by another thread unless it is released by the thread that holds it.

  4. **Circular wait**

     A set of threads are waiting for each other in a circular form.

* Failing to meet any of these 4 conditions mentioned above will prevent a deadlock from happening.



## Deadlock Example

* One example is a deadlock caused by the **inconsistent locking order of Mutex**.

  ```c
  thread1_fn()
  {
      ...
      pthread_mutex_lock(&resource1->mutex);
      ...
      pthread_mutex_lock(&resource2->mutex);	/* thread1 requests resource2 held by thread2 while
      ... 									   holding resource1 (thread1 is blocked) */
      ...
      pthread_mutex_unlock(&resource2->mutex);
      ...
      pthread_mutex_unlock(&resource1->mutex);
      ...
  }
  ```

  ```c
  thread2_fn()
  {
      ...
      pthread_mutex_lock(&resource2->mutex);
      ...
      pthread_mutex_lock(&resource1->mutex);	/* thread2 requests resource1 held by thread1 while
      ... 									   holding resource2 (thread2 is blocked) */
      ...
      pthread_mutex_unlock(&resource1->mutex);
      ...
      pthread_mutex_unlock(&resource2->mutex);
      ...
  }
  ```

  > All four necessary conditions for deadlocks to occur are met in the example above.

  The order of locking the Mutexes must be preserved. There is no such thing as an absolute order to be followed, but the order should be consistent throughout the code. (If in a section of code, locking was in `resource1`, `resource2` order, then anywhere else in the code the locking should happen in the same order.)

  It is recommended to unlock the Mutexes in the LIFO order.






## References

Sagar, A. (2022). *Part A - Multithreading & Thread Synchronization - Pthreads* [Video file]. Retrieved from  https://www.udemy.com/course/multithreading_parta/

