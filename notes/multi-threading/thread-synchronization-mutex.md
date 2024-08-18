[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Multi-Threading (POSIX Threads)</a> > Thread Synchronization - Mutex

# Thread Synchronization - Mutex



## Introduction to Mutex

* Mutex is a thread synchronization construct/tool which provides **mutual exclusion** while a critical section is being access by multiple threads concurrently.

* Analogy:

  Mutex is like a key to a locker and whomever wants to access the locker needs to have the key. Whoever that does not have the key cannot access the locker and has to wait till it obtains the key. Whoever that is accessing the locker needs to handover the key when done accessing the locker.

  Here,

  * Locker = Critical section
  * Whoever = Threads
  * Key = Mutex

* Basic POSIX APIs for Mutex:

  ```c
  pthread_mutex_t mutex;			/* create mutex */
  
  pthread_mutex_lock(&mutex);		/* enter critical section */
  /* critical section */
  pthread_mutex_unlock(&mutex);	/* leave critical section */
  ```


​		See "*POSIX APIs for Mutex*" section below for all the relevant APIs.



## Mutex Rules

* Whichever thread unlocks the Mutex shall be able to enter the critical section.

* If a thread tries to lock an already locked Mutex, the thread turns into blocking state waiting for the Mutex to unlock. Multiple threads may be blocked by the same Mutex. In this case, the OS will decide which thread will gain access to the critical section next depending on several factors such as thread priority, OS scheduling policy, etc.

* When a thread exits the critical section, it MUST unlock the Mutex.

* A thread must not intentionally die while holding the Mutex lock. If so, that Mutex becomes unusable forever.

* If a thread attempts to double lock the same Mutex, it will be self-deadlocked.

  ```c
  pthread_mutex_lock(&mutex); /* lock */
  pthread_mutex_lock(&mutex); /* attempt to lock the same lock again  */
  ```

* Mutexes must be unlocked in a LIFO order (Recommended).

  ```c
  pthread_mutex_lock(&mutex1);
  pthread_mutex_lock(&mutex2);
  /* critical section */
  pthread_mutex_unlock(&mutex2);
  pthread_mutex_unlock(&mutex1);
  ```

* Use of Mutex causes threads to block and unblock. The more the blocking and unblocking of threads, the more the scheduling work overhead on the OS, the poorer the application performance will become. 

  The bigger the size of a critical section, the longer the threads would have to wait in blocking state, the poorer the application performance will become.



## Two Types of Mutex Locking

* Code locking
* Object locking

### Code Locking (Static Locking)

* **Code locking** is used to protect a section of code against the concurrent thread unsafe access. In this case, Mutex is defined at source file level.

  Code locking structure:

  ```c
  static pthread_mutex_t mutex;
  
  foo()
  {
      ...
      pthread_mutex_lock(&mutex);
      /* critical section */
      pthread_mutex_unlock(&mutex);
      ...
  }
  ```

  Code locking example:

  ```c
  char global_buffer[256];	/* shared resource */
  static pthread_mutex_mutex_t mutex;
  
  void pkt_receive(char *pkt, int pkt_size)
  {
      pthread_mutex_lock(&mutex);
      memset(global_buffer, 0, sizeof(global_buffer));
      memcpy(global_buffer_pkt, pkt_size);
      forward_pkt(global_buffer, pkt_size);
      pthread_mutex_unlock(&mutex);
  }
  ```

### Object Locking (Run-time Locking)

* **Object locking** is used to protect an object (e.g., data structure) rather than a section of code against the concurrent thread unsafe access. In this case, Mutex is owned by the object (i.e., one of the members of the object).

  Object locking structure:

  ```c
  foo()
  {
      ...
      pthread_mutex_lock(&object->mutex);
      /* critical section */
      pthread_mutex_unlock(&object->mutex);
      ...
  }
  ```

  Object locking example:

  ```c
  void delete_node_from_list(list l, int n)
  {
      pthread_mutex_lock(&l->mutex);		/* locking an object only */
      node_t *node = search_node(l, n);
      remove_node(node);
      free(node);
      pthread_mutex_unlock(&l->mutex);	/* unlocking an object only */
  }
  ```

* A thread will block other threads ONLY when they are trying to access to an object that it is currently accessing. Otherwise, the same section of code does not have to be protected. 

  For example, if a thread T1 is accessing a linked linked list to delete an element, why should it stop another thread from deleting a node from a binary tree that is completely independent from the linked list T1 is accessing?

  In such a situation, using the code locking will only degrade the application performance.

* When object locking technique is used, a section of code itself can be executed concurrently (single-core) or simultaneously (multi-core) by the threads if the threads are accessing the different objects.



## POXIS APIs for Mutex

* Mutex APIs and generic structure:

  ```c
  /* declaration */
  pthread_mutex_t mutex;
  
  /* initialization */
  pthread_mutex_init(&mutex, NULL);
  
  /* locking & unlocking */
  pthread_mutex_lock(&mutex);
  	/* critical section */
  pthread_mutex_unlock(&mutex);
  
  /* destruction */
  pthread_mutex_destroy(&mutex);
  ```

* Note carefully that Mutex must NEVER be `memcpy()`ed. It will lead to an undefined behavior!

  You should NEVER try to use `memcpy()` to move a data structure from on memory location to another if it has a Mutex as its member.



## Challenge

* **Problem statement:**

  Witness the undefined behavior without **mutual exclusion**. Fix it using Mutex!

  The thread T1 is continuously calculating the sum of the elements of a global array `arr` whose elements are `{1, 2, 3, 4, 5}`, and the T2 is continuously swapping the first and the last element of the array. When you run this program, you'll see the sum displaying 11, 15 or 19 in a seemingly random fashion. This is happening because T1 and T2 are not synchronized, and therefore T1 happens to be calculating the sum on a four different snapshots caused by T2's swapping operation; `{1, 2, 3, 4, 1}`, `{1, 2, 3, 4, 5}`, `{5, 2, 3, 4, 5}`, `{5, 2, 3, 4, 1}`.

  ```c
  /*
   * File Name    : critical_section_problem.c
   * Description  : C program to demonstrate the critical section problem
   * Author       : Modified by Kyungjae Lee 
   *               (Original: Abhishek Sagar - Juniper Networks)
   * Date Created : 01/09/2023
   */
  
  /*
   * compile using:
   * gcc -g -c critical_section_problem.c -o critical_section_problem.o
   * gcc -g critical_section_problem.o -o critical_section_problem -lpthread
   * 
   * run using: 
   * ./critical_section_problem
   */
  
  #include <stdio.h>
  #include <stdlib.h>
  #include <pthread.h> /* POSIX threads*/
  #include <unistd.h>  /* pause(), sleep() */
  #include <errno.h>	 /* errno */
  #include <assert.h>
  
  int arr[] = { 1, 2, 3, 4, 5};
  
  void print_array()
  {
  	int i = 0;
  	int arr_size = sizeof(arr)/sizeof(arr[0]);
  
  	for ( ; i < arr_size -1; i++)		
  		printf("%d ", arr[i]);
  	
  	printf("%d\n", arr[i]);
  }
  
  /* A thread callback fn must have the following prototype 
     void* (*thread_fn)(void*) */
  static void* thread_fn_callback_sum(void *arg) 
  {
  	int i;
  	int sum;
  	
  	int arr_size = sizeof(arr)/sizeof(arr[0]);
  
  	while (1)
      {
  		sum = 0;
  		i = 0;
          
  		while (i < arr_size) 
          {
  			sum += arr[i];
  			i++;
  		}
          
  		printf("sum = %d\n", sum);
  		//print_array();
      }
  }
  
  static void* thread_fn_callback_swap(void *arg) 
  {
  	int temp;
  	int arr_size = sizeof(arr)/sizeof(arr[0]);
  
  	while (1)
      {
  		temp = arr[0];	
  		arr[0] = arr[arr_size -1];
  		arr[arr_size-1] = temp;	
  		//printf("swap :\n");
  		//print_array();
  	}
  }
  
  void sum_thread_create() 
  {
  	/* opaque object, dont bother about its internal members */
  	pthread_t pthread1;
  
  	int rc = pthread_create(&pthread1, NULL, thread_fn_callback_sum, NULL);
      
  	if (rc != 0) 
      {
  		printf("Error occurred, thread could not be created, errno = %d\n", rc);
  		exit(0);
  	}
  }
  
  void swap_thread_create() 
  {
  	/* opaque object, dont bother about its internal members */
  	pthread_t pthread2;
  
  	int rc = pthread_create(&pthread2, 
  				   NULL, 
  				   thread_fn_callback_swap,
  				   NULL);
  	if (rc != 0)
      {
  		printf("Error occurred, thread could not be created, errno = %d\n", rc);
  		exit(0);
  	}
  }
  
  int main(int argc, char *argv[])
  {
  	sum_thread_create();
  	swap_thread_create();
  
  	/*  Allow main thread to exit, while other threads are still running */
  	pthread_exit(0);
      
  	return 0;
  }
  ```
  
* **Solution:**

  ```c
  /* write your code here */
  ```

  



## References

Sagar, A. (2022). *Part A - Multithreading & Thread Synchronization - Pthreads* [Video file]. Retrieved from  https://www.udemy.com/course/multithreading_parta/

