[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Multi-Threading (POSIX Threads)</a> > Thread Cancellation - Introduction

# Thread Cancellation - Introduction



## Introduction to Thread Cancellation

* Often times, a thread in execution needs to be canceled for various reasons. For example:

  * You may want to cancel on-going search operation.
  * You may want to stop sending periodic packets.
  * You may want to stop downloading the file.

  So, thread cancellation is quite common in the programming world!

* Thread cancellation is a lot like telling a person to stop something that he/she is doing.

* Any thread of a process can choose to cancel any other threads of the process.

* Once the thread is canceled, it is terminated; thread cease to exist.

* By default, threads are non-cancellable and calling `pthread_cancel()` API on a non-cancellable will take no efffect. To make the thread cancellable, you need to specify it using `pthread_setcancelstate()` at the beginning of the thread function.

  ```c
  /* set the thread eligible for cancellation */
  pthread_setcancelstate(PTHREAD_CANCEL_ENABLE /*PTHREAD_CANCEL_DISABLE */, 0);
  ```

* Two types of thread cancellation:

  * **Asynchronous cancellation**

    One thread terminates immediately the target thread.

    Can be set to this mode using the API `pthread_setcanceltype()`

    ```c
    pthread_setcanceltype(PTHREAD_CANCEL_ASYNCHRONOUS, 0);
    ```
  
  * **Deferred cancellation**
  
    The target thread checks periodically whether it should terminate, allowing it an opportunity to terminate itself in an orderly fashion. Deferred cancellation has been introduced to address the drawbacks and pitfalls of asynchronous cancellation of threads.
    
    Can be set to this mode using the API `pthread_setcanceltype()`
    
    ```c
    pthread_setcanceltype(PTHREAD_CANCEL_DEFERRED, 0);
    ```
  
* Note that `pthread_setcancelstate()` and `pthread_setcanceltype()` APIs do not take the thread handle as an argument. This implies that the thread that invokes these APIs can only affect itself. In other words, no thread can set the **cancelstate** or the **canceltype** of another thread.





## References

Sagar, A. (2022). *Part A - Multithreading & Thread Synchronization - Pthreads* [Video file]. Retrieved from  https://www.udemy.com/course/multithreading_parta/

