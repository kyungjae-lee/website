[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Multi-Threading (POSIX Threads)</a> > Thread Synchronization - Read/Write Lock

# Thread Synchronization - Read/Write Lock



## Introduction to Read/Write Lock

* A read/write lock (RW lock) allows concurrent access for read-only operations, whereas write operations require exclusive access. This improves performance compared to using regular mutexes.

* Consider three threads in a program; T1 (read-only), T2 (read-only), T3 (write). 

  ```c
  /* T1 (read-only) - computes the sum of all elements of the array */
  int sum(int *arr, int size)
  {
      int i; sum = 0;
      prhtread_mutex_lock(&mutex);
      
      for (i = 0; i < size; i++)
      {
          sum += arr[i];
      }
      
      pthread_mutex_unlock(&mutex);
      return sum;
  }
  ```

  ```c
  /* T2 (read-only) - computes the product of all elements of the array */
  int sum(int *arr, int size)
  {
      int i; mul = 0;
      prhtread_mutex_lock(&mutex);
      
      for (i = 0; i < size; i++)
      {
          sum *= arr[i];
      }
      
      pthread_mutex_unlock(&mutex);
      return sum;
  }
  ```

  ```c
  /* T3 (write) - square each element of the array */
  int sum(int *arr, int size)
  {
      int i;
      prhtread_mutex_lock(&mutex);
      
      for (i = 0; i < size; i++)
      {
          arr[i] *= arr[i];
      }
      
      pthread_mutex_unlock(&mutex);
      return sum;
  }
  ```

  T1 and T2 perform read operations (read-read are non-conflicting operations), hence, should be allowed without blocking each other. However, a regular mutex would not allow this. This program can be improved by using an RW lock instead. It  allows multiple reader threads (e.g., T1, T2) to enter the critical section simultaneously:

  ```c
  /* T1 (read-only) - computes the sum of all elements of the array */
  int sum(int *arr, int size)
  {
      int i; sum = 0;
      rdlock(&rwlock);
      
      for (i = 0; i < size; i++)
      {
          sum += arr[i];
      }
      
      unlock(&rwlock);
      return sum;
  }
  ```

  ```c
  /* T2 (read-only) - computes the product of all elements of the array */
  int sum(int *arr, int size)
  {
      int i; mul = 0;
      rdlock(&rwlock);
      
      for (i = 0; i < size; i++)
      {
          sum *= arr[i];
      }
      
      unlock(&rwlock);
      return sum;
  }
  ```

  ```c
  /* T3 (write) - square each element of the array */
  int sum(int *arr, int size)
  {
      int i;
      wrlock(&rwlock);
      
      for (i = 0; i < size; i++)
      {
          arr[i] *= arr[i];
      }
      
      unlock(&rwlock);
      return sum;
  }
  ```

  Using an RW lock, T1 and T2 can execute in parallel on different CPUs without getting blocked. Great performance improvement!

  A thread need to specify the type of lock it wants to obtain; read lock (`rdlock()`), or write lock (`wrlock()`) on the lock variable (`rwlock`).

* Read-read (non-conflicting operations)

  Write-read, write-write (conflicting operations)



## Read/Write Lock Demonstration

* A test program to demonstrate the POSIX read/write locks.

  ```c
  /*
   * File Name    : rwlock_std_main.c
   * Description  : A test driver for the POSIX standard read/write locks
   * Author       : Modified by Kyungjae Lee (Original: Abhishek Sagar - Juniper Networks)
   * Date Created : 01/14/2023
   */
  
  #include <stdio.h>
  #include <unistd.h>
  #include <assert.h>
  #include <pthread.h>
  
  static pthread_rwlock_t rw_lock;
  static int n_r = 0;		/* counts the number of reader threads executing in the CS */
  static int n_w = 0;		/* counts the number of writer threads executing in the CS */
  pthread_mutex_t state_check_mutex;	/* mutex for operations on the shared variable n_r */
  
  static void cs_status_check()
  {
      pthread_mutex_lock(&state_check_mutex);
      assert(n_r >= 0 && n_w >= 0); /* Cannot be negative */
  
      if (n_r == 0 && n_w == 0)
      {
          // valid condition
      }
      else if (n_r > 0 && n_w == 0)
      {
          // valid condition
      }
      else if (n_r == 0 && n_w == 1)
      {
          // valid condition
      }
      else
      {
          assert(0);
      }
  	
      /* due to the nature of the printf() - using buffer - it also needs to be protected 
         using the mutex (without protection, it will printout meaningless values */
      printf ("n_r = %d, n_w = %d\n", n_r, n_w); /* # of reader & writer threads in the CS */
      pthread_mutex_unlock(&state_check_mutex);
  }
  
  static void execute_cs()
  {
      cs_status_check();
  }
  
  static void reader_enter_cs()
  {
      pthread_mutex_lock(&state_check_mutex);
      n_r++;	/* mutex required since multiple reader threads are allowed to be in the CS */
      pthread_mutex_unlock(&state_check_mutex);
  }
  
  static void reader_leave_cs()
  {
      pthread_mutex_lock(&state_check_mutex);
      n_r--;	/* mutex required since multiple reader threads are allowed to be in the CS */
      pthread_mutex_unlock(&state_check_mutex);
  }
  
  static void writer_enter_cs()
  {
      n_w++;	/* does not need a mutex since a writer thread will always be alone in the CS */
  }
  
  static void writer_leave_cs()
  {
  	n_w--;	/* does not need a mutex since a writer thread will always be alone in the CS */
  }
  
  void* read_thread_fn (void *arg)
  {
      while(1)
      {
          pthread_rwlock_rdlock(&rw_lock);
          reader_enter_cs();	/* to keep track of the # or threads executing in the CS */
         
          execute_cs();
  
          reader_leave_cs();	/* to keep track of the # or threads executing in the CS */
          pthread_rwlock_unlock(&rw_lock);
      }
  }
  
  void* write_thread_fn (void *arg)
  {
      while(1)
      {
      	pthread_rwlock_wrlock(&rw_lock);
          writer_enter_cs();
          
          execute_cs();
  
          writer_leave_cs();
          pthread_rwlock_unlock(&rw_lock);
      }
  }
  
  /* any reader threads can execute in the critical section concurrently, but if 
     a writer thread is in the critical section, no other threads are allowed to get in */
  int main(int argc, char *argv[])
  {
      static pthread_t th1, th2, th3, th4, th5, th6; /* 3 reader, 3 writer threads */
      pthread_rwlock_init(&rw_lock, NULL);
      pthread_mutex_init (&state_check_mutex, NULL);
      pthread_create(&th1, NULL, read_thread_fn, NULL);
      pthread_create(&th2, NULL, read_thread_fn, NULL);
      pthread_create(&th3, NULL, read_thread_fn, NULL);
      pthread_create(&th4, NULL, write_thread_fn, NULL);
      pthread_create(&th5, NULL, write_thread_fn, NULL);
      pthread_create(&th6, NULL, write_thread_fn, NULL);
      pthread_exit(0);
      return 0;
  }
  ```
  
  ```plain
  n_r = 1, n_w = 0
  n_r = 1, n_w = 0
  n_r = 1, n_w = 0
  ...
  n_r = 3, n_w = 0
  n_r = 3, n_w = 0
  n_r = 1, n_w = 0
  n_r = 2, n_w = 0
  n_r = 2, n_w = 0
  ...
  n_r = 3, n_w = 0
  n_r = 3, n_w = 0
  ...
  ```
  
  The point of this program is to show that no two writer threads can be in the critical section simultaneously. To make the output analysis easier:
  
  ```plain
  ./rwlock_std_main > log
  
  $ cat log | grep "n_r = 1, n_w = 0" | wc -l
  785254
  
  $ cat log | grep "n_r = 2, n_w = 0" | wc -l
  3119865
  
  $ cat log | grep "n_r = 3, n_w = 0" | wc -l
  3256646
  
  $ cat log | grep "n_r = 0, n_w = 3" | wc -l
  0
  
  $ cat log | grep "n_r = 0, n_w = 2" | wc -l
  0
  
  $ cat log | grep "n_r = 0, n_w = 1" | wc -l
  452
  ```
  
  > Notice that the case including `n_w = 2` or `n_w = 3` does not exist in the output!



## Implementation of Read/Write Locks

* Properties of POSIX read/write locks:

  1. When **a reader thread** is executing in a critical section, **other reader threads** are still allowed to enter the critical section.
  
  2. When **a writer thread** is executing in a critical section, **no other threads** are allowed to enter the critical section at all.
  
  3. Let the RW lock support the property of **recursiveness**, that is, a thread can lock the RW lock multiple times if necessary.
  
  4. When the RW lock is unlocked, let the OS scheduling policy decide which waiting thread is to enter the critical section next in such a way that no thread suffers from starvation.
  
  5. `assert()` if a thread in the system tries to unlock the already unlocked (or available) RW lock, or unlock the RW lock that is locked by another thread. In such cases, the program must terminate.
  
     Insert as many `assert()`s as possible to ensure that the lock is always in the valid state.
  
  Remember! **Condition variables** and **mutexes** are the basic building blocks of any thread synchronization data structures.
  
* **Interface for Read/Write Locks**

  ```c
  /*
   * File Name    : rwlock.h
   * Description  : Interface for read/write locks
   * Author       : Modified by Kyungjae Lee (Original: Abhishek Sagar - Juniper Networks)
   * Date Created : 01/14/2023
   */
  
  #ifndef RWLOCK_H
  #define RWLOCK_H
  
  #include <pthread.h>
  #include <stdint.h>
  #include <stdbool.h>
  
  typedef struct rwlock_
  {
      /* a mutex to manipulate/inspect the state of rwlock in a mutually exclusive way */
      pthread_mutex_t state_mutex;
      /* a CV to block the the threads when the RW lock is not available */
      pthread_cond_t cv;
      /* count of number of concurrent threads executing inside CS 
         - for writer threads, this value will always be 1 */
      uint16_t n_locks;
      /* no of reader threads waiting for the RW lock grant */
      uint16_t n_reader_waiting;
      /* no of writer threads waiting for the RW lock grant */
      uint16_t n_writer_waiting;
      /* is RW lock currently occupied by reader threads */
      bool is_locked_by_reader;
      /* is RW lock currently occupied by a writer thread */
      bool is_locked_by_writer;
      /* thread handle of the writer thread currently holding the RW lock 
         (it is 0 if the RW lock is not being held by any writer threads */
      pthread_t writer_thread; /* if the RW is locked by a writer thread, which thread is it? */
  } rw_lock_t;
  
  void rw_lock_init (rw_lock_t *rw_lock);
  void rw_lock_rd_lock (rw_lock_t *rw_lock);
  void rw_lock_wr_lock (rw_lock_t *rw_lock);
  void rw_lock_unlock (rw_lock_t *rw_lock) ;
  void rw_lock_destroy(rw_lock_t *rw_lock);
  
  #endif /* RWLOCK_H */
  ```
  
* **Implementation of Read/Write Locks**

  ```c
  /*
   * File Name    : rwlock.c
   * Description  : Implementation of read/write locks
   * Author       : Modified by Kyungjae Lee (Original: Abhishek Sagar - Juniper Networks)
   * Date Created : 01/14/2023
   */
  
  #include <assert.h>
  #include "rwlock.h"
  
  void rw_lock_init (rw_lock_t *rw_lock)
  {
      pthread_mutex_init(&rw_lock->state_mutex, NULL);
      pthread_cond_init(&rw_lock->cv, NULL);
      rw_lock->n_reader_waiting = 0;
      rw_lock->n_writer_waiting = 0;
      rw_lock->is_locked_by_reader = false;
      rw_lock->is_locked_by_writer = false;
      rw_lock->writer_thread = 0;
  }
  
  void rw_lock_rd_lock (rw_lock_t *rw_lock)
  {
      pthread_mutex_lock(&rw_lock->state_mutex);
  
      /* case 1 : rw_lock is available (in unlocked state) */
      if (rw_lock->is_locked_by_reader == false && rw_lock->is_locked_by_writer == false)
      {
          /* double-check the state of rW_lock */
          assert(rw_lock->n_locks == 0);
          assert(!rw_lock->is_locked_by_reader);
          assert(!rw_lock->is_locked_by_writer);
          assert(!rw_lock->writer_thread);
          
          /* update the state of rw_lock (CS protected by state_mutex) */
          rw_lock->n_locks++;	
          rw_lock->is_locked_by_reader = true;	
          
          pthread_mutex_unlock(&rw_lock->state_mutex);
          return;
      }
  
      /* case 2 : rw_lock is locked by reader thread(s) already (recursive lock allowed) */
      if (rw_lock->is_locked_by_reader)
      {    
          /* double-check the state of rw_lock */
          assert(rw_lock->is_locked_by_writer == false);
          assert(rw_lock->n_locks);
          assert(!rw_lock->writer_thread);
          
          /* update the state of rw_lock (CS protected by state_mutex) */
          rw_lock->n_locks++;	
          
          pthread_mutex_unlock(&rw_lock->state_mutex);
          return;
      }
  
      /* case 3 : rw_lock is locked by a writer thread */
      while (rw_lock->is_locked_by_writer)
      {
          /* double-check the state of rw_lock */
          assert(rw_lock->n_locks);	/* a writer thread can lock rw_lock recursively */
          assert(rw_lock->is_locked_by_reader == false);
          assert(rw_lock->writer_thread);
  		       
          rw_lock->n_reader_waiting++;	/* increment waiting threads counter */
          pthread_cond_wait(&rw_lock->cv, &rw_lock->state_mutex);	/* block until available */
          /* when the lock becomes available (upon receiving a signal on CV) */
          rw_lock->n_reader_waiting--;	/* wakeup and decrement waiting thread counter */
      }
  
      /* will reach here only when the previous while loop condition becomes false, 
         meaning that rw_lock is at least not locked by any writer thread (rw_lock
         may still be locked by one or more reader threads) */
      if (rw_lock->n_locks == 0)
      {
          /* first reader enters CS */
          rw_lock->is_locked_by_reader = true;
      }
      rw_lock->n_locks++;
      assert (rw_lock->is_locked_by_writer == false);
      assert(!rw_lock->writer_thread);
      pthread_mutex_unlock(&rw_lock->state_mutex);
  }
  
  void rw_lock_unlock (rw_lock_t *rw_lock)
  {
      pthread_mutex_lock(&rw_lock->state_mutex);
  
      /* case 1 : rw_lock is already in unlocked state */
      assert(rw_lock->n_locks);	/* do not allow unlocking an already unlocked rw_lock */
  
      /* case 2 : a writer thread trying to unlock rw_lock which is locked by a writer thread */
      if (rw_lock->is_locked_by_writer)
      {
          /* only the owner of the rw_lock must attempt to unlock rw_lock */
          assert(pthread_self() == rw_lock->writer_thread);
          assert(rw_lock->is_locked_by_reader == false);
  
          rw_lock->n_locks--; /* unlock rw_lock */
          
          if (rw_lock->n_locks)
          {
              /* the current writer thread still owns rw_lock (more unlocks required to
                 completely release rw_lock) */
              pthread_mutex_unlock(&rw_lock->state_mutex);
              return;
          }
  
          /* at this point, the current writer thread has no more to unlock */
          if (rw_lock->n_reader_waiting || rw_lock->n_writer_waiting)
          {
              /* broadcast instead of signal in case there are multiple 'reader' threads
                 waiting to enter the critical section */
              pthread_cond_broadcast(&rw_lock->cv);
              /* either all waiting reader threads will get in the critical section, 
                 or exactly one of the waiting writer thread(s) will get in */
          }
  
          rw_lock->is_locked_by_writer = false;
          rw_lock->writer_thread = 0;
          pthread_mutex_unlock(&rw_lock->state_mutex);
          return;
      }
  
      /* case 3 : reader thread trying to unlock the rw_lock which is locked by a reader thread
         - case 3 cannot be moved before case 2 (leave it to you for reasoning) */
  
      /* There is minor design flaw in our implementation. Not actually flaw, but
      a room to mis-use the rw_locks which is a trade off for simplicity. Here Simplicity is
      that rw_lock implementation do not keep track of "who" all reader threads have
      obtain the read lock on rw_lock. Here is an example, how rw_lock implementation
      could be erroneously used by the developer, yet rw_lock implementation would not
      report a bug ( assert () ).
      Suppose, threads T1, T2 and T3 owns read locks on rw_lock. and Now, some
      Thread T4 which do not own any type of lock on rw_lock invoke rw_lock_unlock( )
      API. The API would still honor the unlock request, since our rw_lock do not keep
      track of who all "reader" threads owns the rw_lock and treat T4 as some reader thread
      trying to unlock the rw_lock */
  
      else if (rw_lock->is_locked_by_reader)
      {
          assert(rw_lock->is_locked_by_writer == false);
          assert(rw_lock->writer_thread == 0);
  
          rw_lock->n_locks--;
  
          if (rw_lock->n_locks)
          {
              /* if rw_lock is not completely unlocked yet */
              pthread_mutex_unlock(&rw_lock->state_mutex);
              return;
          }
  
          /* at this point, the current read thread has no more to unlock */
          if (rw_lock->n_reader_waiting || rw_lock->n_writer_waiting)
          {
              pthread_cond_broadcast(&rw_lock->cv);
          }
  
          rw_lock->is_locked_by_reader = false;
          pthread_mutex_unlock(&rw_lock->state_mutex);
          return;
      }
  
      assert(0);
  }
  
  void rw_lock_wr_lock (rw_lock_t *rw_lock)
  {
      pthread_mutex_lock(&rw_lock->state_mutex);
  
      /* case 1 : rw_lock is available (in unlocked state) */
      if (rw_lock->is_locked_by_reader == false && rw_lock->is_locked_by_writer == false)
      {
          /* double-check the state of rw_lock */
          assert(rw_lock->n_locks == 0);
          assert(!rw_lock->is_locked_by_reader);
          assert(!rw_lock->is_locked_by_writer);
          assert(!rw_lock->writer_thread);
          
          /* update the state of rw_lock (CS protected by state_mutex) */
          rw_lock->n_locks++;
          rw_lock->is_locked_by_writer = true;
          rw_lock->writer_thread = pthread_self();
          
          pthread_mutex_unlock(&rw_lock->state_mutex);
          return;
      }
  
      /* case 2 : rw_lock is locked by a writer thread (itself) already 
        (recursive lock) */
      if (rw_lock->is_locked_by_writer && rw_lock->writer_thread == pthread_self())
      {
          /* double-check the state of rw_lock */
          assert(rw_lock->is_locked_by_reader == false);
          assert(rw_lock->n_locks);
  
          /* update the state of rw_lock (CS protected by state_mutex) */
          rw_lock->n_locks++; /* implements the recursive lock by the same thread */
          pthread_mutex_unlock(&rw_lock->state_mutex);
          return;
      }
  
      /* case 3 : rw_lock is already locked by any other thread(s) than the current thread */
      while (rw_lock->is_locked_by_reader || rw_lock->is_locked_by_writer)
      {
          /* sanity checks */
          if (rw_lock->is_locked_by_reader)
          {
              assert(rw_lock->is_locked_by_writer == false);
              assert(rw_lock->n_locks);
              assert(rw_lock->writer_thread == 0);
          } 
          else if (rw_lock->is_locked_by_writer)
          {
              assert(rw_lock->is_locked_by_reader == false);
              assert(rw_lock->n_locks);
              assert(rw_lock->writer_thread);
          }
  
          /* at this point, the current thread is trying to obtain the rw_lock which is not
             available */
          rw_lock->n_writer_waiting++;
          pthread_cond_wait(&rw_lock->cv, &rw_lock->state_mutex); /* block until available */
          rw_lock->n_writer_waiting--;
      } /* while loop instead of if statement to prevent spurious wakeup */
  
      /* now rw_lock is available and the current writer thread owns (locks) rw_lock */
      assert(rw_lock->is_locked_by_reader == false);
      assert(rw_lock->is_locked_by_writer == false);
      assert(rw_lock->n_locks == 0);
      assert(!rw_lock->writer_thread);
      
      rw_lock->is_locked_by_writer = true;
      rw_lock->n_locks++;
      rw_lock->writer_thread = pthread_self();
      pthread_mutex_unlock(&rw_lock->state_mutex);
  }
  
  /* it is the developer's responsibility to invoke this API on the RW lock object only when
     it is 100% ensured that it is not being used by any threads in the system */
  void rw_lock_destroy(rw_lock_t *rw_lock)
  {
      /* safety-check before destroy */
      assert(rw_lock->n_locks == 0); /* no threads should be executing in the CS */
      assert(rw_lock->n_reader_waiting == 0); /* no threads should be blocked on the RW lock */
      assert(rw_lock->n_writer_waiting == 0); 
      assert(rw_lock->is_locked_by_writer == false); /* RW lock is not locked by any threads */
      assert(rw_lock->is_locked_by_reader == false);
      assert(!rw_lock->writer_thread); 
      
      /* destroy */
      pthread_mutex_destroy(&rw_lock->state_mutex);	/* destroy mutex */
      pthread_cond_destroy(&rw_lock->cv);				/* condition variable */
  }
  ```
  
* **Test Driver for Read/Write Locks**

  ```c
  /*
   * File Name    : rwlock_main.c
   * Description  : A test driver for the user defined read/write locks
   * Author       : Modified by Kyungjae Lee (Original: Abhishek Sagar - Juniper Networks)
   * Date Created : 01/14/2023
   */
  
  #include <stdio.h>
  #include <unistd.h>
  #include <assert.h>
  #include <pthread.h>
  
  static pthread_rwlock_t rw_lock;
  static int n_r = 0;		/* counts the number of reader threads executing in the CS */
  static int n_w = 0;		/* counts the number of writer threads executing in the CS */
  pthread_mutex_t state_check_mutex;	/* mutex for operations on the shared variable n_r */
  
  static void cs_status_check()
  {
      pthread_mutex_lock(&state_check_mutex);
      assert(n_r >= 0 && n_w >= 0); /* Cannot be negative */
  
      if (n_r == 0 && n_w == 0)
      {
          // valid condition
      }
      else if (n_r > 0 && n_w == 0)
      {
          // valid condition
      }
      else if (n_r == 0 && n_w == 1)
      {
          // valid condition
      }
      else
      {
          assert(0);
      }
  	
      /* due to the nature of the printf() - using buffer - it also needs to be protected 
         using the mutex (without protection, it will printout meaningless values */
      printf ("n_r = %d, n_w = %d\n", n_r, n_w); /* # of reader & writer threads in the CS */
      pthread_mutex_unlock(&state_check_mutex);
  }
  
  static void execute_cs()
  {
      cs_status_check();
  }
  
  static void reader_enter_cs()
  {
      pthread_mutex_lock(&state_check_mutex);
      n_r++;	/* mutex required since multiple reader threads are allowed to be in the CS */
      pthread_mutex_unlock(&state_check_mutex);
  }
  
  static void reader_leave_cs()
  {
      pthread_mutex_lock(&state_check_mutex);
      n_r--;	/* mutex required since multiple reader threads are allowed to be in the CS */
      pthread_mutex_unlock(&state_check_mutex);
  }
  
  static void writer_enter_cs()
  {
      n_w++;	/* does not need a mutex since a writer thread will always be alone in the CS */
  }
  
  static void writer_leave_cs()
  {
  	n_w--;	/* does not need a mutex since a writer thread will always be alone in the CS */
  }
  
  void* read_thread_fn (void *arg)
  {
      while(1)
      {
          pthread_rwlock_rdlock(&rw_lock);
          reader_enter_cs();	/* to keep track of the # or threads executing in the CS */
         
          execute_cs();
  
          reader_leave_cs();	/* to keep track of the # or threads executing in the CS */
          pthread_rwlock_unlock(&rw_lock);
      }
  }
  
  void* write_thread_fn (void *arg)
  {
      while(1)
      {
      	pthread_rwlock_wrlock(&rw_lock);
          writer_enter_cs();
          
          execute_cs();
  
          writer_leave_cs();
          pthread_rwlock_unlock(&rw_lock);
      }
  }
  
  /* any reader threads can execute in the critical section concurrently, but if 
     a writer thread is in the critical section, no other threads are allowed to get in */
  int main(int argc, char *argv[])
  {
      static pthread_t th1, th2, th3, th4, th5, th6; /* 3 reader, 3 writer threads */
      pthread_rwlock_init(&rw_lock, NULL);
      pthread_mutex_init (&state_check_mutex, NULL);
      pthread_create(&th1, NULL, read_thread_fn, NULL);
      pthread_create(&th2, NULL, read_thread_fn, NULL);
      pthread_create(&th3, NULL, read_thread_fn, NULL);
      pthread_create(&th4, NULL, write_thread_fn, NULL);
      pthread_create(&th5, NULL, write_thread_fn, NULL);
      pthread_create(&th6, NULL, write_thread_fn, NULL);
      pthread_exit(0);
      return 0;
  }
  ```

* If the program gets terminated by one of those `assert()`s, it means the program is buggy. Also, check if any programmer is using the APIs the wrong way!






## References

Sagar, A. (2022). *Part A - Multithreading & Thread Synchronization - Pthreads* [Video file]. Retrieved from  https://www.udemy.com/course/multithreading_parta/
