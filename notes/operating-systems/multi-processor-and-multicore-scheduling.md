[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Multiprocessor and Multicore Scheduling

# Multiprocessor and Multicore Scheduling



## Introduction

* New issues arise in scheduling when more than 1 processor or core is available.

* Some systems are capable of treating threads (a.k.a. light-weight processes) as individually schedulable units (like processes).

  * Switching threads incurs less overhead than switching processes.

* First, we will review the categories of multiprocessors and types of **granularity** (concurrent programming). Then we'll review process scheduling options for the moderately concurrent systems (i.e., general purpose operating systems). 

  Super computer, high-level parallelism and so on are not discussed at this point.



## Multiprocessor System Classification

* **Loosely Coupled (a.k.a. Distributed Multiprocessor or Cluster)**
  * Consists of a collection of relatively autonomous system.
  * Each processor has its own main memory and I/O channels/devices.
  * Distributed systems are managed by special purpose distributed operating systems (including cloud systems). This type of OS manages distributed machines as cohesive machines.
* **Functionally Specialized Processors**
  
  * Specialized processors are controlled by the main processor and provide services to it.
  * e.g., I/O processors (disk controller), graphics processors, etc.
* **Tightly Coupled Multiprocessor**
  
  * A set of processors that share a common main memory and are under the integrated control of a single operating system.
  * The OS controls which process runs on which CPU.
  * e.g., A motherboard that contains multiple CPUs on it, a chip that contains multiple processor cores, etc.
  
  This section is all about "Tightly Coupled Multiprocessor" system.



## Granularity

* A term related to th level of synchronization required to compute parallel tasks. It is also a measure of **concurrency** in software systems. (How much concurrency is in the software task?)

  | Grain Size  | Description                                                  | Synchronization Interval (Instructions) |
  | ----------- | ------------------------------------------------------------ | --------------------------------------- |
  | Fine        | Parallelism inherent in a single instruction stream          | < 20                                    |
  | Medium      | Parallel processing or multitasking within a single application | 20 - 200                                |
  | Coarse      | Multiprocessing of concurrent processes in a multiprogramming environment | 200 - 2000                              |
  | Very Coarse | Distributed processing across network nodes to form a single computing environment | 2000 - 1M                               |
  | Independent | Multiple unrelated processes                                 | Not applicable                          |

### Defining Granularity

* **Independent Parallelism**

  * There is no explicit synchronization among processes.
  * Each represents a separate, independent application or job.
  * This is a typical time-sharing system or general purpose desktop system. (e.g., Windows)
    * The OS still needs to ensure the mutual exclusion when dealing with the shared resources. (The shared resources problems we've discussed in the uniprocessor system still holds in the multiprocessor system.)
  * A multiprocessor provides the same services as a multiprogrammed uniprocessor (i.e., You can schedule processes to run, only now you have multiple CPUs.)
  * Improves average response time to users due to having extra capacity.
* **Coarse / Very Coarse_Grained Parallelism**
  * There is synchronization among processes, but at a gross level.

  * Each process is part of a set of concurrent processes, cooperating to perform a task or set of tasks. The OS is making decisions on dispatching processes to CPUs.

  * Can be used on a multiprocessor system with few or no changes to software that was used in the uniprocessor system.

  * e.g., Producer and consumer model

  * If interaction (communication) between processes is very little, then a distributed system can provide the support (e.g., SETI SEARCH)

* **Medium-Grained Parallelism**
  * A single application can be implemented as a collection of threads (a.k.a. light-weight processes).

  * The programmer is usually defining explicit parallelism.

  * High degrees of coordination or synchronization is used. There are frequent interactions among the processes or threads in the task.

  * The operating system must consider the interactions going on between the threads or processes when making decisions on when to dispatch one to a processor. (We are extending the dispatch schemes from the uniprocessor system to the multiprocessor system.)

* **Fine-Grained Parallelism**
  * Find grained parallelism is used in highly specialized (hardware) systems and programming languages.
  * Parallelism is performed at the instruction level.
    * This means that the same instruction is being executed on all of the processors at the same time - in lock-step.

  * e.g., supercomputers, vector processors, etc. Many problems, such as weather forecasting and image processing benefit from this fine-grained level of parallelism.
  * Highly specialized operating system is necessary.




## Scheduling on a Multiprocessor

* Here, we talk about the scheduling decisions made for **coarse** and **medium grained** concurrent processes or threads. (The distributed OS and the statement level parallelism are covered by advanced operating systems courses.)
* Scheduling design must consider the following issues:
  1. Assignment of processes to processors
  2. Use of multiprogramming on individual processors
  3. Dispatching of a process

### 1. Assignment of Processes to Processors

* Assumption - The architecture of the multiprocessor is uniform. Therefore, all processors are the same and can be treated as a pool of resources. Assign processes on demand. (Any processor can run any process in the system.)

* Should the assignment of a process to a processor be static or dynamic?

  * Static - A process stays with its assigned processor.
    * Each processor has its own queue of processes.
    * Advantages - Can take the advantage of the cache associated with the processor and minimize the context switching overhead.
    * Disadvantages - Not possible to utilize the potentially idle processor since a process assigned to a processor cannot be dispatched to another processor.
  * Dynamic - Use a global Ready queue instead.
    * Works well when memory is shared by all processors
    * Advantages - Can keep all the processors busy. (CPU utilization $\uparrow$)
    * Disadvantages - Higher overhead in context switching.

  Linux uses a hybrid approach - Each processor has its local queue of processes, the OS periodically load-balances to relieve back log or improve utilization.

* Two and a hybrid approaches as to how the OS execute in this type of multiprocessor system:
  * **Master-Minion Approach**
    * OS kernel functions always run on a dedicated processor (or a core).
    * Other processes may run on the other processors (or cores).
    * The master manages all other processes. (The master is responsible for scheduling.)
    * When a process needs an OS service it sends a request to the master processes and waits.
    * Advantages - Simple to implement.
    * Disadvantages - The whole system fails if the core running the OS kernel fails. The master can become a performance bottleneck when there are more requests than it can handle. 
  * **Peer Approach**
    * The kernel functions (or processes) can execute on any processor. 
    * In fact, the functions can be executed in multiple processes that can be scheduled on any processor and moved. A specific OS kernel process can be placed on the same CPU on which a process that needs that specific service of the OS kernel runs.
    * A processor selects processes from the global queue of processes. The global queue must ensure the mutual exclusive access so that no two processes attempt to take the same process at the same time.
    * Advantages - Even if one core fails, the whole system may not fail.
    * Disadvantages - Overhead?
  * **Hybrid Approaches**
    * There can be combinations of the traits from the two approaches to form different hybrid approaches to assigning processes to processor

### 2. Multiprogramming on Individual Processors

This is about whether or not a CPU can be multiprogrammed.

* When processes are assigned to ready queues, the next question is should that processor be multiprogrammed? (i.e., interleave multiple processes on that core?)
* It is fairly easy to make decision for coarse-grained parallelism. Because each task is an individual process, they can use the same context switching as a uniprocessor.

### 3. Process Dispatching

* Which process should be dispatched from the Ready queue to the Running queue(s)?
* In coarse-grained systems where there is not much interaction between processes or threads, it actually becomes much less important to "perfect" the dispatching algorithm. Because you have more processors to work with. Just pick the next available process or threads in the ready queue!
* First Come First Serve with a priority scheme works very well in a system like this. Why?
  * In a uniprocessor system, FCFS showed poor performance because the execution of short processes can be delayed by one long process running on the CPU.
  * However, in a multiprocessor system, this problem no longer exists because even if a long process is running on one CPU, the following processes can be dispatched to the next available CPUs without having to wait the long process to finish.



## Consider Medium-Grained Concurrency

* In medium grained concurrency, often a task is a collection of cooperating, light-weight processes or threads. Dispatching in this environment can take some different approaches than basic FCFS, because you want to ensure that the threads that are working together perform well together.
* There are 4 basic approaches:
  1. Load Sharing
  2. Gang Scheduling
  3. Dedicated Processor Assignment
  4. Dynamic Scheduling

### 1. Load Sharing

* In this approach processes are not assigned to a particular processor. Each processor, when idle, selects a thread or process from a **global queue** of ready tasks.
* The global Ready queue can be maintained using any scheduling policies such as FCFS, smallest process next, priority, and so on. Most widely used, though, is the FCFS added with priority feature. (Does not mean "optimal" but performs very well.)
* Advantages - The load is distributed evenly across the CPUs. No centralized processor is necessary and you can keep all processors busy. (CPU utilization $\uparrow$)
* Disadvantages - Ensuring mutual exclusive access to the global ready queue can cause the bottleneck. Also, threads move between the CPUs so higher overhead of context switching.

### 2. Gang Scheduling

* A refinement of the load sharing approach where:
  * The OS maintains a local ready queue for each processor AND a shared ready queue.
  * A local processor queue is used by threads that are bound to a specific processor. Allows a processor to give preference to bound threads, but choose ready processes when it is idle.
  * Lowers the cost of context switching when threads are discouraged from moving to a different core. And it groups related threads near each other.

### 3. Dedicated Processor Assignment

* Basically an extreme form of gang scheduling.
* You can dedicate processors to an application. Used in highly parallel systems.
* Eliminates process switching among cores (i.e., eliminates context switching overhead), but also removes the ability to load balance.

### 4. Dynamic Scheduling

* Allow the programmer to communicate with the OS via programming languages to perform their own thread scheduling.



## Examples of Scheduling: Linux & Windows

### Linux Scheduling

* Provides real-time and non-real-time scheduling.
* FIFO - first in first out system for real-time threads, and a RR - round robin system for real-time threads.
* Higher priorities preempt lower priorities.
* Non-real-time threads have a set of priorities in a lower band than real-time.
* Maintains a queue of processes PER processor. Each processor can perform basic round robin techniques to time slice the running processes.
* Periodically load balances by moving processes among processors.

### Windows Scheduling

* Designed to be responsive to needs of a single user in a highly interactive environment.
* 2 priority bands: real-time and variable.
* Threads requiring immediate attention (such as communications) are in real-time band. And have higher priority over variable band.
* Higher priority threads preempt lower priority.
* Threads may run on any processor. A ready thread is dispatched to the next available core (load sharing).
* Attempts to assign a ready thread to the last processor it ran on in the hopes that its data is still in that local cache.





## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.

