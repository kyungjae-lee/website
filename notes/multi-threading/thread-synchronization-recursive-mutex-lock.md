[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Multi-Threading (POSIX Threads)</a> > Thread Synchronization - Recursive Mutex/Lock

# Thread Synchronization - Recursive Mutex/Lock



## Introduction to Recursive Mutex

* A regular mutex, which has already been locked cannot be locked again by the same thread. Any attempt for a thread to lock a mutex that has already been locked by itself leads to **deadlock**. 

* A **recursive mutex** works just like a regular mutex except that it can be locked by the same thread multiple times. A thread has to unlock the mutex as many times as it has locked it.

* Defining a recursive mutex using POSIX Standard APIs:

  ```c
  pthread_mutex_t mutex;
  pthread_mutexattr attr;
  
  pthread_mutexattr_init(&attr);
  pthread_mutexattr_settype(&attr, PTHREAD_MUTEX_RECURSIVE);
  pthread_mutex_init(&mutex, &attr);
  ```

  > Everything is the same as when you are defining a regular mutex, except that for a recursive mutex, you need to specify its property as a recursive mutex by passing `PTHREAD_MUTEX_RECURSIVE` to the function `pthread_mutexattr_settype()` as a second argument.



## Use Case of Recursive Mutex

* Recursive mutexes simply things in the scenario where there is a possibility that a function could be invoked from another function which locks the mutex that will be locked again by the invoked function.

  For example, let's say a developer wrote the following function:

  ```c
  void delete_student_record(student_db, stud_rec)
  {
      pthread_mutex_lock(&student_db.mutex);
      student_db.delete(student_db, stud_rec);
      pthred_mutex_unlock(&student_db.mutex);
  }
  ```

  And, after a few months, a new developer writes the following function without knowing the implementation of `delete_student_record()`:

  ```c
  void remove_failed_student(student_db, int agg_limit)
  {
      stud_rec_t *stud_rec;
      pthread_mutex_lock(&student_db.mutex);	/* locking the mutex */
      for_each_rec(student_db, stud_rec)
      {
          if (stud_rec->agg_limit < agg_limit)
          {
              delete_student_record(student_db, stud_rec); /* includes mutex locking mechanism */
          }
      }
      pthread_mutex_unlock(&student_db.mutex);
  }
  ```

  In this case, invoking `remove_failed_student()` function is likely to cause a deadlock! However, had this mutex been defined as a recursive mutex, no issue would have risen.



## Implementation of Recursive Mutex

* Being able to implement our own recursive mutexes will be helpful when writing a multi-threaded program using a language that does not support recursive mutexes. Try to make the interface and the functionalities of your own recursive mutex as similar to the POSIX pthread library as possible.
* Requirements:
  * If thread T1 has locked a recursive mutex (RM), any other thread T2 must block upon an attempt to lock the RM.
  * T2 must resume execution only when T1 (holding a lock), releases all locks on RM.
  * RM, like a regular Mutex, must guarantee the mutual exclusion.
  * Owning thread must invoke as many unlock calls as lock calls to release the RM.
  * Assert if any thread tries to unlock an unlocked RM.


* **Interface for Recursive Mutex**

  ```c
  /*
   * File Name    : recursive_mutex.h
   * Description  : Interface for recursive mutex
   * Author       : Modified by Kyungjae Lee (Original: Abhishek Sagar - Juniper Networks)
   * Date Created : 01/14/2023
   */
  
  #ifndef RECURSIVE_MUTEX
  #define RECURSIVE_MUTEX
  
  #include <pthread.h>
  #include <stdint.h>
  #include <stdbool.h>
  
  typedef struct rec_mutex_
  { 
      uint16_t n;					/* mumber of times self-lock is allowed */
      pthread_t locking_thread;	/* pthread id of the thread which owns this mutex */
      pthread_cond_t cv;			/* a CV to make threads block */
      pthread_mutex_t state_mutex;/* a Mutex to manupulate the state of this structure 
      							   in a mutually exclusive way */
      uint16_t n_waiting;	 		/* number of threads waiting for the grant of this mutex lock */
  } rec_mutex_t;
  
  void rec_mutex_init(rec_mutex_t *rec_mutex) ;
  void rec_mutex_lock(rec_mutex_t *rec_mutex);
  void rec_mutex_unlock(rec_mutex_t *rec_mutex);
  void rec_mutex_destroy(rec_mutex_t *rec_mutex);
  
  #endif /* RECURSIVE_MUTEX */
  ```

* **Implementation of Recursive Mutex**

  ```c
  /*
   * File Name    : recursive_mutex.c
   * Description  : Implementation of recursive mutex
   * Author       : Modified by Kyungjae Lee (Original: Abhishek Sagar - Juniper Networks)
   * Date Created : 01/14/2023
   */
  
  #include <stdio.h>
  #include <assert.h>
  #include "recursive_mutex.h"
  
  void rec_mutex_init(rec_mutex_t *rec_mutex)
  {
      rec_mutex->n = 0;
      rec_mutex->locking_thread = 0;
      pthread_cond_init(&rec_mutex->cv, NULL);
      pthread_mutex_init(&rec_mutex->state_mutex, NULL);
      rec_mutex->n_waiting = 0;
  }
  
  void rec_mutex_lock(rec_mutex_t *rec_mutex)
  {
      /* to make modifying rec_mutex operation an atomic operation */
      pthread_mutex_lock(&rec_mutex->state_mutex);
      
      /* case 1 : when rec_mutex has NOT yet been locked */
      if (rec_mutex->n == 0)
      {
          assert(rec_mutex->locking_thread == 0); /* double-check if locking_thread == 0 */
          rec_mutex->n = 1;
          rec_mutex->locking_thread = pthread_self();
          pthread_mutex_unlock(&rec_mutex->state_mutex);
          return;
      }
  
      /* case 2 : when rec_mutex has already been locked by itself */
      if (rec_mutex->locking_thread == pthread_self())
      {
          assert(rec_mutex->n);	/* double-check if rec_mutex > 0 */
          rec_mutex->n++;
          pthread_mutex_unlock(&rec_mutex->state_mutex);
          return;
      }
  
      /* case 3 : when rec_mutex has been locked by some other thread */
      while (rec_mutex->locking_thread && rec_mutex->locking_thread != pthread_self())
      {
          /* simply make the current thread block on this rec_mutex */
          rec_mutex->n_waiting++;
          pthread_cond_wait(&rec_mutex->cv, &rec_mutex->state_mutex); /* this thread will 
          	wakeup once its condition variable receives the signal */
          rec_mutex->n_waiting--;
      }
  
      /* case 3 cont'd - now locking rec_mutex is available for the current thread */
      assert(rec_mutex->n == 0);
      assert(rec_mutex->locking_thread == 0);
  
      rec_mutex->n = 1;
      rec_mutex->locking_thread = pthread_self();
      pthread_mutex_unlock(&rec_mutex->state_mutex);
  }
  
  void rec_mutex_unlock(rec_mutex_t *rec_mutex)
  {
      pthread_mutex_lock(&rec_mutex->state_mutex);
      
      /* case 1 : when rec_mutex has NOT yet been locked 
         (unlocking an already unlocked rec_mutex) */
      if (rec_mutex->n == 0)	
      {
          assert(rec_mutex->locking_thread == 0);
          assert(0);
      }
  
      /* case 2 : when rec_mutex has already been locked by itself */
      if (rec_mutex->locking_thread == pthread_self())
  	{
      	assert(rec_mutex->n);	/* double-check if rec_mutex > 0 */
          rec_mutex->n--;
  
          if (rec_mutex->n > 0)
          {
              pthread_mutex_unlock(&rec_mutex->state_mutex);
              return;
          }
  
         	/* is there any blocked threads waiting to obtain rec_mutex? */
          if (rec_mutex->n_waiting)
          {
              /* if so, send a signal for one among those blocked (waiting) threads */
          	pthread_cond_signal(&rec_mutex->cv);
          }
  
          rec_mutex->locking_thread = 0;
          pthread_mutex_unlock(&rec_mutex->state_mutex);
      	return;
      }
  
      /* case 3 : when rec_mutex has been locked by some other thread */
      while (rec_mutex->locking_thread && rec_mutex->locking_thread != pthread_self())
      {
          /* why would a thread want to unlock the rec_mutex that belongs to someone else? */
          assert(0);	/* terminate the program! */
  	}
  }
  
  void rec_mutex_destroy(rec_mutex_t *rec_mutex)
  {
      /* the following asserts are important because destroying a rec_mutex when there is any
         thread holding it or waiting to obtain it will results in an undefined behavior
         (imagine a thread waiting for a signal on the condition variable that does not even
         exist) */
      assert(!rec_mutex->n); /* there must be no threads still holding rec_mutex */
      assert(!rec_mutex->locking_thread); /* there must be no threads still holding rec_mutex */
      assert(!rec_mutex->n_waiting); /* there must be no threads waiting to obtain rec_mutex */
      
      /* only if above conditions have successfully been met, destory rec_mutex */ 
      pthread_mutex_destroy(&rec_mutex->state_mutex);
      pthread_cond_destroy(&rec_mutex->cv);
  }
  ```
  
  > Notice that `assert()`s have been placed so that the program terminates as soon as it encounters a bug. Feel free to insert any many `assert()`s as possible so that bugs can be found as soon as possible!
  >
  > L46: `while()` instead of `if()` to prevent spurious wakeups.
  >
  > It is the developer's responsibility to only invoke `rec_mutex_destroy()` in a situation that everything is safe to be destroyed.






## References

Sagar, A. (2022). *Part A - Multithreading & Thread Synchronization - Pthreads* [Video file]. Retrieved from  https://www.udemy.com/course/multithreading_parta/
