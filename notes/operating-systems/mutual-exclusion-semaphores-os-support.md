[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Mutual Exclusion - Semaphores (OS Support)

# Mutual Exclusion - Semaphores (OS Support)



## Introduction

* In order to implement more modular, sophisticated waiting strategies, that eliminate busy-wait and starvation, we need structures that the OS can be involved with. We can allow the OS to assist with wait times using the state-transitions and events rather than relying on processes to idle in inefficient/costly busy wait loops.
* Semaphore, proposed by Edsger Dijktra, is a technique to manage concurrent process by using a special variable type (i.e., **semaphore**) with an integer value. 
* Semaphores are used with shared memory systems, processes can each utilize the shared semaphores.
* Both OS and high-level languages provide support for the semaphore in code.

### Semaphore types

* Depending on the maximum number of processes (or threads) allowed to enter the critical section:

  * **Counting/general semaphore** - >= 1

  * **Binary semaphore** - 1 (special case of counting/general semaphore)

* Depending on the order in which the processes blocked on the semaphore get moved to the ready queue:

  * **Strong semaphore** - FIFO (fair, starvation-free)

  * **Weak semaphore** - Unspecified order (may suffer from starvation)

  [!] Note: Semaphores each have a queue of waiting (blocked) processes.



## Semaphore

* **Semaphore Operations**

  * **Initialize**
    * A semaphore may be initialized to a non-negative integer
    * Binary semaphores start with 1, counting semaphores >= 1

  * **Wait**
    * Decrements the semaphore value as an **atomic operation**
    * If the value becomes negative the process issuing the wait becomes blocked (not a busy wait)
    * Otherwise, the process has gained control of the semaphore and continues its execution

  * **Signal**
    * Increments the semaphore value as an **atomic operations**
    * If the value is still <= 0, then a blocked process that was waiting for the semaphore will be unblocked (i.e., moved into the ready queue) so it can proceed into the critical section when it is dispatched.

  > A binary semaphore with value 1, means the resource is available
  >
  > A counting semaphore with a value of N means that N instances of the resource are available, or N more processes can enter the critical section
  >
  > If the semaphore value is 0 then the next process will have to block
  >
  > If the semaphore value is < 0, it represents the number of blocked process

* **A Definition of Counting/General Semaphore Primitives**

  ```c
  struct semaphore
  {
      int count;
      queue_type queue;
  }
  
  void sem_wait(semaphore s)
  {
      s.count--;
      if (s.count < 0)
      {
          // place this process in s.queue
          // block this process
      }
  }
  
  void sem_signal(semaphore s)
  {
      s.count++;
      if (s.count <= 0)
      {
          // remove a blocked process from s.queue and place it on the ready queue
      }
  }
  ```

* **A Definition of Binary Semaphore Primitives**

  ```c
  struct binary_semaphore
  {
      enum {ZERO, ONE} value;
      queue_type queue;
  }
  
  void bsem_wait(binary_semaphore s)
  {
      if (s.value == ONE)
          s.value = ZERO;
      else
      {
          // place this process in s.queue
          // block this process
      }
  }
  
  void bsem_signal(binary_semaphore s)
  {
      if (s.queue is empty())
          s.value = ONE;
      else
      {
          // remove a blocked process from s.queue and place it on the ready queue
      }
  }
  ```

  > A binary semaphore just checks for 0 or 1 rather than incrementing and decrementing.
  >
  > Unlike a counting semaphore, a binary semaphore does not have way to tell how many processes are blocked on it. No matter how many blocked processes there are, the binary semaphore value will be kept as 0.

* **Mutual Exclusion Using Semaphores**

  ```c
  // mutual exclusion using a semaphore
  const int n = /* number of processes */;
  semaphore s = 1;	// semaphores must be initialized upon creation
  
  void p(int i)
  {
      while (true)
      {
          sem_wait(s);
          // critical section
          sem_signal(s);
          // remainder
      }
  }
  
  void main()
  {
      parbegin(p(1), p(2), ... , p(n));
      // the construct 'parbegin(p1, p2, ... , Pn)' means, suspend the execution of the main program;
      // initiate concurrent execution of procedures P1, P2, ... , Pn;
      // when all of P1, P2, ... , Pn have terminated, resume the main program
  }
  ```
  



## Demonstration - Semaphores

* Let's say a section of code (critical section) is protected by a semaphore of initial value 5. Then, first 5 processes (or threads) will each enter the critical section without any problem decrementing the semaphore value. From this point on, no new process is allowed to get in until and unless any process in the critical section comes out and signals. The 6^th^ process, upon arrival, will see that the semaphore value is 0, and realize the critical section is already full. Decrementing the semaphore value, the 6^th^ process will move to the BLOCKED queue of the semaphore to wait for any process to come out of the critical section. (Any processes that arrive after the 6^th^ process will do the same and place themselves in the BLOCKED queue.). When a process comes out of the critical section, it will signal (increment the semaphore value by 1), and a process waiting in the BLOCKED queue, if any, will be moved to the READY queue so when it is dispatched it can enter the critical section.
* Don't be confused about the semaphore value. Even if the value is less than 0 at certain point in time, some processes may be leaving and some may be entering the critical section.






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
