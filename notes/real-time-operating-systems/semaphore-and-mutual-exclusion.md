[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Real-Time Operating Systems (RTOS)</a> > Semaphore & Mutual Exclusion

# Semaphore & Mutual Exclusion



## Mutual Exclusion

* Mutual exclusion is a synchronization technique that ensures only one process or thread can access a critical section at any given time, preventing concurrent access and potential conflicts.
* A critical section, in general, includes shared entities such as:
  * Global variable
  * Data structure
  * Common function which uses a global data
  * Peripheral memory, etc.
* Both **mutex** and **semaphore** services can be used to implement the mutual exclusion, but using mutexes can be more beneficial than using semaphores.



## Semaphore

* A semaphore is a kernel object (or kernel service), that one or more threads of execution can acquire or release for the purpose of **synchronization** or **mutual exclusion**.

* Imagine a scenario where there are two tasks, Task-A (producer) and Task-B (consumer), are operating as follows:

  ```plain
  @t1: Task-A runs first and it waits for data from device driver
  @t2: Task-B runs, does nothing since there's no data to consume
  @t3: Task-A runs again and finds out there's still no data from device driver
  @t4: Task-B runs, does nothing since there's still no data to consume
  ```

  In this scenario, Task-B preempts Task-A even if it has nothing to do. This leads to inefficient CPU usage, which can be improved by implementing some type of communication or signaling between Task-A and Task-B for synchronization.

* The following kernel objects can be used for synchronization:

  * Events (or Event Flags)
  * Semaphores (Counting and Binary)
  * Queues and Message Queues
  * Pipes
  * Mailboxes
  * Signals (UNIX like signals)
  * Mutex

  FreeRTOS supports **semaphores**, **queues**, and **mutexes**.

* Any task that fails to obtain the key upon accessing the resource will be blocked, and the blocked tasks are then sent to the "**Task Waiting List**", which manages them either in a "FIFO" manner or on a "highest priority first" basis.



## Types of Semaphore

### Binary semaphore

* Value can only be either 0 or 1, allowing only one thread to access the resource

* Use cases

  1. Synchronization between tasks

     ```c
     void task1_producer(void)
     {
         if (TRUE == produce_some_data())
         {
             /* Signal Task2 if it was previously blocked due to unavailability of the key.
                'GIVE' operation will increment the semaphore value by 1. */
             sema_give(&sema_key);
         }
     }
     
     void task2_consumer(void)
     {
         /* If seme_key is unavailable, then Task2 will be blocked. */
         /* If sema_key is available, then Task2 will take it and sema_key will become 
            unavailable. */
         while (sema_take(&sema_key))
         {
             /* Task will reach this point only when 'TAKE' operation is successful */
             /* Consume data */
             /* Since sem_key is zero at this point, next time when Task2 will try to 'TAKE'
                it will be blocked */
         }
     }
     ```

     > When the data is produced, Task1 (Producer) will Increment the key. (The key unblocks Task2 (Consumer) if it was previously blocked due to unavailability of the key.)
     >
     > Once Task2 unblocks, it will check the availability of the key, and if the key is available, Task2 will consume the data.

  2. Synchronization between interrupts-and-tasks

  3. Mutual exclusion for guarding a critical section

### Counting semaphore

* Value can be greater than 1, allowing more than one thread to access the resource

* Use cases

  1. Event/interrupt counting & handling

  2. Resource management

     In this case, the count value indicates the number of available resources, and to gain control of a resource, a task must first acquire a semaphore by decrementing its counting value. When the count value reaches zero, it signifies that no free resources are available, and when a task is done using a resource, it releases the semaphore, thereby incrementing the semaphore's count value.



## Summary

* Synchronization is nothing but aligning a number of tasks to achieve a desired behavior.
* Mutual exclusion is avoiding a task to execute the critical section which is already owned by another task for execution.
* Typically semaphores are used to implement the synchronization between tasks and between tasks-and-interrupts.
* Mutexes are the best choice to implement the mutual exclusion. (Semaphores can also be used to implement the mutual exclusion but it will introduce some serious design issues.)





## References

Nayak, K. (2022). *Mastering RTOS: Hands on FreeRTOS and STM32Fx with Debugging* [Video file]. Retrieved from https://www.udemy.com/course/mastering-rtos-hands-on-with-freertos-arduino-and-stm32fx/

