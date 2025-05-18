[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Interview Questions</a> > Embedded Systems

# Embedded Systems



## What are Embedded Software Best Practices?

1. **Write Clear and Readable Code**
    Use meaningful names, consistent formatting, and comments to make code understandable.
2. **Use Version Control**
    Track changes and collaborate safely with tools like Git.
3. **Modular Design**
    Break code into reusable, independent modules or functions.
4. **Static and Dynamic Testing**
    Perform code reviews, static analysis, and thorough testing (unit, integration, system).
5. **Handle Hardware Interactions Carefully**
    Manage interrupts, peripherals, and timing constraints properly.
6. **Resource Management**
    Optimize memory, CPU, and power usage — critical in embedded systems.
7. **Avoid Blocking Calls**
    Use non-blocking or interrupt-driven designs to maintain responsiveness.
8. **Defensive Programming**
    Validate inputs, check error codes, and handle unexpected conditions gracefully.
9. **Documentation**
    Maintain clear documentation for code, interfaces, and hardware requirements.
10. **Follow Coding Standards**
     Use industry standards like MISRA C for safety-critical systems.



## What is a Reentrant Function in Embedded Systems?

A reentrant function is a function that can be used concurrently by multiple tasks without the risk of data corruption. It can be interrupted at any time and safely resumed later without data loss.

Reentrant functions either use only local variables or protect shared data with synchronization tools such as semaphores or mutexes when global variables are involved.



## What is a deadlock?

A **deadlock** is a situation in which two or more tasks are **permanently blocked**, each waiting for a resource that the other holds, and **none can proceed**. It typically occurs in multitasking systems when tasks compete for shared resources and do not release them properly.



## What is an RTOS?

A **Real-Time Operating System (RTOS)** is an operating system designed to **run tasks predictably and reliably within strict timing constraints**. It is commonly used in embedded systems where **timing is critical**, such as automotive systems, medical devices, industrial control, and robotics.

Key features of an RTOS:

* **Deterministic behavior**: Tasks are executed in a predictable manner.
* **Real-time scheduling**: Prioritizes tasks based on urgency (e.g., preemptive priority-based scheduling).
* **Multitasking**: Supports running multiple tasks seemingly in parallel.
* **Low latency**: Quick response to interrupts and high-priority tasks.
* **Inter-task communication and synchronization**: Uses tools like **queues, semaphores, mutexes**.

Real-time types:

* **Hard real-time**: Missing a deadline is unacceptable (e.g., pacemaker).
* **Soft real-time**: Occasional deadline misses are tolerable (e.g., video streaming).



## RTOS vs. GPOS

**RTOS** is ideal when **timing, predictability, and reliability** are crucial.

**GPOS** is suitable for **user-friendly, high-performance systems** without strict real-time constraints.

| Feature                    | **RTOS**                                           | **GPOS** (e.g., Windows, Linux, macOS)              |
| -------------------------- | -------------------------------------------------- | --------------------------------------------------- |
| **Timing guarantees**      | Predictable, deterministic response times          | Best-effort, not time-deterministic                 |
| **Task scheduling**        | Real-time scheduling (usually priority-based)      | Fair scheduling (time-sharing, round-robin, etc.)   |
| **Latency**                | Very low (few microseconds)                        | Higher (milliseconds typical)                       |
| **Interrupt handling**     | Fast, prioritized                                  | Slower, often delayed by background tasks           |
| **Resource usage**         | Lightweight, optimized for limited resources       | Heavy, assumes availability of memory and CPU power |
| **Multitasking**           | Preemptive with strict priorities                  | Preemptive, but not always priority-deterministic   |
| **Use cases**              | Embedded systems, medical devices, automotive, IoT | Desktops, laptops, servers, mobile phones           |
| **Reliability under load** | Consistent even under heavy load                   | Can become unpredictable under high load            |
| **Example OS**             | FreeRTOS, Zephyr, VxWorks, TI-RTOS                 | Windows, Linux, macOS, Android                      |



## What is an MMU?

An MMU (Memory Management Unit) is a hardware component that **translates virtual addresses to physical addresses** in a system. It enables features like:

- Virtual memory
- Memory protection
- Process isolation

This allows each program to run in its own memory space, improving **security**, **stability**, and **efficiency** in multitasking systems.



## What is a Cache?

A cache is a small, faster memory than RAM that stores **frequently accessed data/instructions** to speed up access times. It sits between the **CPU and main memory (RAM)** and helps reduce the time it takes to read or write data. (Improves system performance by reducing memory access latency.)

For example, if the CPU needs the same data multiple times, it can quickly retrieve it from the cache instead of slower RAM.



## What is a Scheduler?

A scheduler is a core component of an operating system (especially in RTOS and GPOS) that **decides which task or process runs next** on the CPU.

Key functions:

* **Selects the highest priority or most eligible task** to run
* **Manages CPU time** among multiple tasks
* Ensures **fairness, efficiency**, or **real-time responsiveness**, depending on the system

In RTOS:

* Often uses **priority-based preemptive scheduling**
* Ensures **real-time deadlines** are met

In GPOS:

* Uses **time-sharing or round-robin** techniques
* Aims for **user responsiveness and throughput**