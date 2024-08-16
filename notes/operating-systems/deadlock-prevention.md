[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Deadlock Prevention

# Deadlock Prevention



## Deadlock Prevention Strategy

* Design a system in such a way that the possibility of deadlock is excluded
* Two main methods:
  * **Indirect** - Prevent the occurrence of one of the three necessary conditions
  * **Direct** - Prevent the occurrence of a circular wait



## Deadlock Condition Prevention

### Eliminate "Mutual Exclusion"

* This is NOT a possible solution as mutual exclusion cannot be disallowed in a concurrent system with shared resources.
* If access to a resource requires mutual exclusion, then mutual exclusion must be supported by the OS. Some resources, such as files, may allow multiple accesses for reads but only exclusive access for writes. Even in this case, deadlock can occur if more than one process requires write permission.

### Eliminate "Hold and Wait"

* Can be prevented by requiring that a process request all of its required resources at one time (i.e., upfront) and blocking the process until all requests can be granted simultaneously (i.e., Do not begin until and unless you are granted all the resources required to finish your job!)
* This approach is inefficient in the following 4 ways:
  * A process may be held up for a long time waiting for all of its resource requests to be filled, when in fact it could have proceeded with only some of the resources.
  * Resources allocated to a process may remain unused for a considerable period, during which time they are denied to other processes.
  * A process may not know in advance all of the resources that it will require.
  * In a modular programming, multi-threaded environment, an application would need to be aware of all resources that will be requested at all levels or in all modules to make the simultaneous request.

### Eliminate "No Preemption"

* If a process holding certain resources is denied a further request, that process must release its original resources and, if necessary, request them again together with the additional resource.
* Alternatively, if a process requests a resource that is currently held by another process, the OS may preempt the second process and require it to release its resources. (This scheme would prevent deadlock only if no two processes possessed the same priority.)
* This approach is practical only when applied to resources whose state can be easily saved and restored later, as is the case with a processor.

### Eliminate "Circular Wait"

* The circular wait condition can be prevented by defining a linear ordering of resource types.

* Processes must request and be granted those resources in the proper order. 

  e.g., If a process has been allocated resources of type R, then it may subsequently request only those resources of types following R in the ordering.



## Drawbacks of Deadlock Prevention

* Deadlock prevention strategies are conservative and result in inefficient utility of resources.
* Another strategy is **deadlock avoidance**.






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
