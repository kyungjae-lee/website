[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Real-Time Operating Systems (RTOS)</a> > Introduction to RTOS

# Introduction to RTOS



## What is "Real-Time"?

* Myth - The Real-Time Computing is equivalent to fast computing (X)

  Truth - The Real-Time Computing is equivalent to **predictable** computing (O)

* A real-time system guarantees **timeliness execution** of given tasks (i.e., response time is guaranteed), NOT the raw speed of execution. Having more processors, more RAM, faster bus interfaces does NOT make a system real-time.

* In a real-time system, the correctness of the computations depends upon both of the followings:

  * The logical correctness of the computation
  * The time at which the result is produced 

  Note that failing to meet the timing constraints will also be considered a FAILURE in a real-time system.



## What is a Real-Time Application (RTA)?

* RTAs are not necessarily the fast-executing applications.

* RTAs are **time-deterministic** applications, which means that their **response time to events is almost constant**.

* **Soft real-time applications** (Tolerant to small delays)

  There could be a small deviation in response time, in terms of milliseconds or seconds, and it will not be considered a failure.

  e.g., VoIP application, Stock market website

* **Hard real-time applications** (Not tolerant to any delays)

  MUST complete within a given time limit and failure to do so will result in an absolute failure of the system. 

  e.g., Airbag deployment system, Anti-lock breaking system, Missile guidance and control system

* To run a real-time application, a real-time operating system is necessary.



## What is a Real-Time Operating System (RTOS)?

* An RTOS is an OS specially designed to run applications with very precise timing and a high degree of reliability.
* To be considered as "real-time", an OS must have a known maximum time for each of the critical operations that it performs. Some of these operations include:
  * Handling of interrupts and internal system exceptions
  * Handling of critical sections
  * Scheduling mechanisms, etc.
    * In general, RTOS scheduling policy favors high-priority tasks.



## RTOS and Multitasking

* A **task** in a real-time system is a unit of individual functionality, that is, a piece of code which can be scheduled/dispatched on the CPU to execute.

  e.g., Possible tasks in a temperature monitoring application can be:

  - Sensor data gathering

  - Updating display

  - User input processing

    ...

* There are different ways to achieve multitasking.

  * In a single-core system, multitasking can be achieved by using a **scheduler**. (Concurrency)
  * In a multi-core system, multiple tasks can run on multiple cores simultaneously. (Parallelism)

  In an embedded environment, single-core system widely used so task scheduling is very important. Effective and fast scheduling gives a user an illusion that multiple tasks are running simultaneously.





## References

Nayak, K. (2022). *Mastering RTOS: Hands on FreeRTOS and STM32Fx with Debugging* [Video file]. Retrieved from https://www.udemy.com/course/mastering-rtos-hands-on-with-freertos-arduino-and-stm32fx/

