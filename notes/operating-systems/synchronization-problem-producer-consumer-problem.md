[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Synchronization Problem - Producer/Consumer Problem

# Synchronization Problem - Producer/Consumer Problem



## Introduction

* In dealing with the design synchronization and concurrency mechanisms, it is useful to be able to relate the problem at hand to known problems, and to be able to test any solution in terms of its ability to solve these  known problems.
* One of the several problems that appear frequently is the **producer/consumer problem**. (Another is, readers/writers problem)



## Producer/Consumer Problem

* **Definition**

  One or more producers generating some type of data (records, characters) and placing them into the buffer. A consumer is removing items from the buffer, one at a time.

  Need to ensure that

  * Only one producer or consumer may access the buffer at a time.
  
  * A producer does not try to add to a full buffer.
  * A consumer does not try to take from an empty buffer.



## Solutions to the Producer/Consumer Problem Using Semaphores

### Solutions to the Infinite-Buffer Producer/Consumer Problem

* An **INCORRECT** solution to the Infinite-Buffer Producer/Consumer Problem using binary semaphores:

  ```c
  /* An incorrect solution to the Infinite-Buffer Producer/Consumer Problem using binary semaphores. */
  
  int n;
  binary_sempahore s = 1; /* sempahore to protect operations on buffer and variable 'n' */
  binary_semaphore delay = 0; /* 0 means buffer is empty so consumer should delay consuming */
  
  void producer()
  {
      while (true)
      {
          produce();
          semWaitB(s);
          append();
          n++;
          if (n == 1)
              semSignalB(delay); /* let the consumer know that there is an item in the buffer */
          semSignalB(s);
      }
  }
  
  void consumer()
  {
      semWaitB(delay);
      
      while (true)
      {
          semWaitB(s);
          take();
          n--;
          semSignalB(s);
          consume();
          if (n == 0)
              semWaitB(delay); /* if buffer is empty, transition to blocking state until there is
              					the buffer becomes non-empty */
      }
  }
  
  void main()
  {
      n = 0;
      parbegin(producer, consumer);
  }
  ```

  > The problem occurs when `n` gets updated between L29 and L32 due to the preemption of the producer. For this solution to work correctly, it must be guaranteed that updated made to `n` in L29 must be preserved until it gets tested in L32.

  Possible scenario for the problem:

  |      | Producer                        | Consumer                      | s    | n      | Delay |
  | ---- | ------------------------------- | ----------------------------- | ---- | ------ | ----- |
  | 1    |                                 |                               | 1    | 0      | 0     |
  | 2    | `semWaitB(s)`                   |                               | 0    | 0      | 0     |
  | 3    | `n++`                           |                               | 0    | 1      | 0     |
  | 4    | `if (n == 1) semSignalB(delay)` |                               | 0    | 1      | 1     |
  | 5    | `semSignalB(s)`                 |                               | 1    | 1      | 1     |
  | 6    |                                 | `semWaitB(delay)`             | 1    | 1      | 0     |
  | 7    |                                 | `semWaitB(s)`                 | 0    | 1      | 0     |
  | 8    |                                 | `n--`                         | 0    | 0      | 0     |
  | 9    |                                 | `semSignalB(s)`               | 1    | 0      | 0     |
  | 10   | `semWaitB(s)`                   |                               | 0    | 0      | 0     |
  | 11   | `n++`                           |                               | 0    | 1      | 0     |
  | 12   | `if (n == 1) semSignalB(delay)` |                               | 0    | 1      | 1     |
  | 13   | `semSignalB(s)`                 |                               | 1    | 1      | 1     |
  | 14   |                                 | `if (n == 0) semWaitB(delay)` | 1    | 1      | 1     |
  | 15   |                                 | `semWaitB(s)`                 | 0    | 1      | 1     |
  | 16   |                                 | `n--`                         | 0    | 0      | 1     |
  | 17   |                                 | `semSignalB(s)`               | 1    | 0      | 1     |
  | 18   |                                 | `if (n == 0) semWaitB(delay)` | 1    | 0      | 0     |
  | 19   |                                 | `semWaitB(s)`                 | 0    | 0      | 0     |
  | 20   |                                 | `n--`                         | 0    | **-1** | 0     |
  | 21   |                                 | `semSignalB(s)`               | 1    | **-1** | 0     |

  > Notice that before `n` in L8 can even be tested by the subsequent `if` statement, the producer kicks in in L10 and updates `n` in its code. When `n` became 0 in L8, `semWaitB(delay)` should've been called by the subsequent `if` statement, but it didn't happen and this inconsistency results in an invalid value for `n`, which is -1.

* A **CORRECT** solution to the Infinite-Buffer Producer/Consumer Problem using binary semaphores:

  To correct the above mentioned problem, you can introduce an additional variable `m` to `consumer()` code to capture the value of `n`:

  ```c
  /* A correct solution to the Infinite-Buffer Producer/Consumer Problem using binary semaphores. */
  
  ...
      
  void consumer()
  {
      semWaitB(delay);
      
      while (true)
      {
          semWaitB(s);
          take();
          n--;
          m = n; /* capture the value of 'n' */
          semSignalB(s);
          consume();
          if (m == 0) /* no matter what, 'm' still contains the correct 'n' value to be tested! */
          	semWaitB(delay);
      }
  }
  
  ...
  ```

* A solution to the Infinite-Buffer Producer/Consumer Problem using counting semaphores:

  ```c
  /* A solution to the Infinite-Buffer Producer/Consumer Problem using counting semaphores */
  
  semaphore n = 0, s = 1;
  
  void producer()
  {
      while (true)
      {
          produce();
          semWait(s);
          append();
          semSignal(s);
          semSignal(n);
      }
  }
  
  void consumer()
  {
      while (true)
      {
          semWait(n);
          semWait(s);
          take();
          semSignal(s);
          consume();
      }
  }
  
  void main()
  {
      parbegin(producer, consumer);
  }
  ```

### A Solution to the Bounded-Buffer Producer/Consumer Problem

* A solution to the Bounded-Buffer Producer/Consumer Problem using counting semaphores:

  ```c
  const int sizeofbuffer = /* buffer size */;
  semaphore s = 1, n = 0, e = sizeofbuffer;
  
  void producer()
  {
      while (true)
      {
          produce();
          semWait(e);
          semWait(s);
          append();
          semSignal(s);
          semSignal(n);
      }
  }
  
  void consumer()
  {
      while (true)
      {
          semWait(n);
          semWait(s);
          take();
          semSignal(s);
          semSignal(e);
          consume();
      }
  }
  
  void main()
  {
      parbegin(producer, consumer);
  }
  ```






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
