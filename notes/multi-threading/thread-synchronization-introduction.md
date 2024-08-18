[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Multi-Threading (POSIX Threads)</a> > Thread Synchronization - Introduction

# Thread Synchronization - Introduction



## Introduction to Thread Synchronization

* The hardest and the most important aspect of multi-threading is the **thread synchronization**. Thread synchronization is the task of coordinating the execution of threads in a way that no two threads can have access to the same shared data and resources.

* Thread synchronization is required in multi-threaded programs whenever multiple threads compete to perform conflicting operations (i.e., Read-Write or Write-Write) on a shared resource. (Read-Read is not a conflicting operation.) Otherwise, the behavior is undefined.

  Shared resources:

  * Heap data structures
  * Global variables
  * File descriptors (opened files, sockets)
  * Receiving data from external sources via multiple inlets

* What problems would occur if we didn't have thread synchronization?

  Consider a multi-threaded process P having two threads T1 and T2, and P maintains a linked list of integers. Thread T1 and T2 are scheduled on the CPU(s) in any order so we cannot and must not assume the order in which the threads are going to be executed.

  ```c
  /* in thread T1 */
  node_t* get_node_from_list(int n)
  {
      node_t *node = search_node(list, n);
      return node;
  }
  ```

  ```c
  /* in thread T2 */
  void delete_node_from_list(int n)
  {
      node_t *node = search_node(list, n);
      remove_node(node);
      free(node);
  }
  ```

  Imagine what would happen if T1 gets preempted by T2 in the middle of executing the function `get_node_from_list()` and T2 completes executing the function `delte_node_from_list()` before releasing the CPU back to T1. T1 may end up returning the address which has already been freed by T2. This is an example of the Read(T1)-Write(T2) operation.

* Concurrent access of shared resources (e.g., data structures) between multiple threads opens a window during which data is inconsistent. The root cause is **concurrency**.

* A section of code within a process where a shared resource is accessed by multiple threads are called the **critical section**. A critical section must be an atomic operation.

* Goal = Concurrency + Data Consistency

* Thread synchronization is nothing but identifying the critical sections and applying several techniques to prevent unprotected concurrent access to shared resources by several threads of the process.



## Critical Section

* A section of code within a process where a shared resource is accessed by multiple threads are called the **critical section**. For example:

  ```c
  int global_val = 100;	/* shared variable */
  
  thread_fn()
  {
  	...
      global_var1++;
      ...
  }

* A critical section must be executed by the concurrent threads but **by one and only one thread at a time**. In other words, a critical section must be an **atomic operation**. 

  If the critical sections are allowed to be accessed by multiple threads without deploying appropriate thread synchronization techniques, the application will, sooner or later, fail. 





## References

Sagar, A. (2022). *Part A - Multithreading & Thread Synchronization - Pthreads* [Video file]. Retrieved from  https://www.udemy.com/course/multithreading_parta/

