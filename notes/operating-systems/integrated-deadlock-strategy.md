[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Integrated Deadlock Strategy

# Integrated Deadlock Strategy



## Summary of 3 Deadlock Handling Strategies

| Approach   | Characterized by                                             | Different schemes                                            | Advantages                                                   | Disadvantages                                                |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Prevention | $\cdot$ most conservative<br>$\cdot$ undercommits $R$s       | $\cdot$ requesting all $R$s at once                          | $\cdot$ works well for $Ps$ that perform a single burst of activity<br>$\cdot$ no preemption necessary | $\cdot$ inefficient<br>$\cdot$ delays process initiation<br>$\cdot$ future resource requirements must be known by processes |
|            |                                                              | $\cdot$ preemption                                           | $\cdot$ works well with $R$s that are easy to save and restore state | $\cdot$ preempts more often than necessary (may lead to starvation) |
|            |                                                              | $\cdot$ resource ordering                                    | $\cdot$ can be enforced at compile-time<br>$\cdot$ does not need a complex run-time computation since problem is solved in system design | $\cdot$ disallows different orders (need to request in certain order) |
| Avoidance  | $\cdot$ less conservative than prevention, more conservative than detection (allows for higher resource utilization) | $\cdot$ Banker's algorithm (compute safe-state prior to completing a $R$ grant) | $\cdot$ no preemption necessary                              | $\cdot$ future $R$ requirements must be known by OS<br>$\cdot$ $P$s can be blocked for long periods (starvation) |
| Detection  | $\cdot$ least conservative (always grant when available - allows for highest $R$ utilization) | $\cdot$ periodically compute (i.e., algorithm) for deadlock  | $\cdot$ never delays process initiation<br>$\cdot$ facilitates online handling | $\cdot$ inherent preemption losses                           |



## An Integrated Deadlock Strategy

* There are strengths and weaknesses to all of the strategies for dealing with deadlock. Rather than attempting to design an OS facility that employs only one of these strategies, it might be more efficient to use different strategies in different situations.

* One suggested approach:

  * Group resources into a number of different resource classes.

  * Use the linear ordering strategy defined previously for the prevention of circular wait to prevent deadlocks between resource classes.

  * Within a resource class, use the algorithm that is most appropriate for that class.

    e.g., Consider the following classes of resources and the strategies could be used:

    * **Swappable space** (Blocks of memory on secondary storage for use in swapping processes)

      Prevention of deadlocks by requiring that all of the required resources that may be used be allocated at one time, as in the hold-and-wait prevention strategy. This strategy is reasonable if the maximum storage requirements are known, which is often reasonable if the maximum storage requirements are known, which is often the case. 

      Deadlock avoidance is also a possibility.

    * **Process resources** (Assignable devices, such as tape drives, and files)

      Avoidance will often be effective in this category, because it is reasonable to expect processes to declare ahead of time the resources that they will require in this class.

      Prevention by means of resource ordering within this class is also possible.

    * **Main memory** (Assignable to processes in pages or segments)

      Prevention by preemption appears to be the most appropriate strategy for main memory. When a process is preempted, it is simply swapped to secondary memory, freeing space to resolve the deadlock.

    * **Internal resources** (Such as I/O channels)

      Prevention by means of resource ordering can be used.






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
