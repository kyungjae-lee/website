[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Real-Time Scheduling

# Real-Time Scheduling



## Real-Time Systems

* The operating system, and in particular the scheduler, is perhaps the most important component.

  Examples:

  * Control of laboratory experiments
  * Process control in industrial plants
  * Robotics
  * Air traffic control
  * Telecommunications
  * Military command and control systems

* Correctness of the system depends not only on the **logical result of the computation** but also on the **time at which the results are produced**.

* Tasks or processes attempt to control or react to events that take place in the outside world.

* These events occur in "real-time" and tasks must be able to keep up with them.



## Hard & Soft Real-Time Tasks

* **Hard real-time task**
  * One that must meet its deadline
  * Otherwise it will cause unacceptable damage or a fatal error to the system.
* **Soft real-time task**
  * Has an associated deadline that is desirable but not mandatory
  * It still makes sense to schedule and complete the task even if it has passed its deadline.



## Periodic & Aperiodic Tasks

* **Periodic tasks**
  * Requirement may be stated as:
    * Once per period $T$
    * Exactly $T$ units apart
* **Aperiodic tasks**
  * Has a deadline by which it must finish or start
  * May have a constraint on both start and finish time



## Characteristics of Real-Time Systems

* 5 requirements of the real-time operating systems:
  * Determinism
  * Responsiveness
  * User control
  * Reliability
  * Fail-soft operation

### Determinism

* Concerned with how long an operaing system delays before acknowledging an interrupt.
* Operations are performed at fixed, predetermined times or within predetermined time intervals.
* The extent to which an operating system can deterministically satisfy requests depends on:
  * The speed with which it can respond to interrupts
  * Whether the system has sufficient capacity to handle all requests within the required time

### Responsiveness

* Together with determinism make up the response time to external events.
  * Critical for real-time systems that must meet timing requirements imposed by individuals, devices, and data flows external to the system.
  * Concerned with how long, after acknowledgement, it takes an operating system to service the interrupt.
* Responsiveness includes:
  * Amount of time required to initially handle the interrupt and begin execution of the interrupt service routine (ISR)
  * Amount of time required to perform the ISR
  * Effect of interrupt nesting

### User Control

* Generally much broader in a real-time operating system than in ordinary operaing systems.
* It is essential to allow the user fine-grained control over task priority.
* User should be able to distinguish between hard and soft tasks and to specify relative priorities within each class.
* May allow user to specify such characteristics as:
  * Paging or process swapping
  * What processes must always be resident in main memory
  * What disk transfer algorithms are to be used
  * What rights the processes in various priority bands have

### Reliability

* More important for real-time systems than non-real-time systems.
* Real-time systems respond to and control events in real time so loss or degradation of performance may have catastrophic consequences such as:
  * Financial loss
  * Major equipment damage
  * Loss of life

### Fail-Soft Operation

* A characteristic that refers to the ability of a system to fail in such a way as to preserve as much capability and data as possible.
* Important aspect is **stability**.
  * A real-time system is stable if the system will meet the deadlines of its most critical, highest-priority tasks even if some less critical task deadlines are not always met.
* The following features are common to most real-time OSs:
  * A stricter use of priorities than in an ordinary OS, with preemptive scheduling that is designed to meet real-time requirements.
  * Interrupt latency is bounded and relatively short.
  * More precise and predictable timing characteristics than general purpose OSs.



## Real-Time Scheduling

* Real-time scheduling approaches depend on:
  * Whether a system performs schedulability analysis
    * If it does, whether it is done statically or dynamically
  * Whether the result of the analysis itself produces a scheduler plan according to which tasks are dispatched at run-time

### Classes of Real-Time Scheduling Algorithms

* **Static table-driven approaches**
  * Performs a static analysis of feasible schedules of dispatching.
  * Result is a schedule that determines, at run-time, when a task must begin.
* **Static priority-driven preemptive approaches**
  * A static analysis is performed but no schedule is drawn up.
  * Analysis is used to assign priorities to tasks so that a traditional priority-driven preemptive scheduler can be used.
* **Dynamic planning-based approaches**
  * Feasibility is determined at run-time rather than offline prior to the start of execution.
* **Dynamic best effort approaches**
  * No feasibility analysis is performed
  * System tries to meet all deadlines and aborts any started process whose deadline is missed.

### Scheduling Algorithms

* **Static table-driven scheduling**
  * Applicable to tasks that are periodic
  * Input to the analysis consists of the periodic arrival time, execution time, periodic ending deadline, and relative priority of each task.
  * This is a predictable approach but one that is inflexible because any change to any task requirements requires that the schedule be redone.
  * Earliest-deadline-first or other periodic deadline techniques are typical of this category of scheduling algorithms.
* **Static priority-driven preemtive scheduling**
  * Makes use of the priority-driven preemptive scheduling mechanism common to most non-real-time multiprogramming systems.
  * In a non-real-time system, a variety of factors might be used to determine priority.
  * In a real-time system, priority assignment is related to the time constraints associated with each task.
  * One example of this approach is the rate monotonic algorithm which assigns static priorities to tasks based on the length of their periods.
* **Dynamic planning-based scheduling**
  * After a task arrives, but before its execution begins, an attempt is made to create a schedule that contains the previously scheduled tasks as well as the new arrival.
  * If the new arrival can be scheduled in such a way that its deadlines are satisfied and that no currently scheduled task misses a deadline, then the schedule is revised to accommodate the new task
* **Dynamic best effort scheduling**
  * The approach used by many real-time systems that are currently commercially available.
  * When a task arrives, the system assigns a priority based on the characteristics of the task.
  * Some form of deadline scheduling is typically used.
  * Typically the tasks are aperiodic so no static scheduling analysis is possible.
  * The major disadvantage of this form of scheduling is, that until a deadline arrives or until the task completes, we do not know whether a timing constraint will be met.
  * Its advantage is that it is easy to implement.



## Priority Inversion

* Can occur in any priority-based preemptive scheduling scheme.
* Particularly relevant in the context of real-time scheduling.
* Occurs when circumstances within the system force a higher priority task to wait for a lower priority task.
* Unbounded priority inversion:
  * The duration of a priority inversion depends not only on the time required to handle a shared resource, but also on the unpredictable actions of other unrelated tasks.
* e.g., The "Mars Pathfinder" mission.






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
