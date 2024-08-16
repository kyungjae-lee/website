[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Deadlock Detection

# Deadlock Detection



* Deadlock prevention strategies are very conservative; they solve the problem of deadlock by limiting access to resources and by imposing restrictions on processes.

* At the opposite extreme, **deadlock detection** strategies do not limit resource access or restrict process actions. With deadlock detection, requested resources are granted to processes whenever possible. Periodically, the OS performs an algorithm that allows it to detect the circular wait.



## Deadlock Detection Algorithm

* A check for deadlock can be made as frequently as each resource request, or less frequently, depending on how likely it is for a deadlock to occur.

* Consider a system of $n$ processes and $m$ different types of resources. Let us define the following vectors and matrices:

  * **Resource** vector - total amount of each resource in the system

    $R = (R_1, R_2, ... , R_m)$

  * **Available** vector - total amount of each resource not allocated to any process

    $V =(V_1, V_2, ... , V_m)$

  * **Request** matrix - $Q_{ij}$ = amount of resources of type $j$ requested by process $i$
    $$
    Q =
    \begin{pmatrix}
    Q_{11} & Q_{12} & ... & Q_{1m} \\
    Q_{21} & Q_{22} & ... & Q_{2m} \\
    ... & ... & ... & ... \\
    Q_{n1} & Q_{n2} & ... & Q_{nm} \\
    \end{pmatrix}
    $$

  * **Allocation** matrix- $A_{ij}$ = current allocation to process $i$ of resource $j$
    $$
    A =
    \begin{pmatrix}
    A_{11} & A_{12} & ... & A_{1m} \\
    A_{21} & A_{22} & ... & A_{2m} \\
    ... & ... & ... & ... \\
    A_{n1} & A_{n2} & ... & A_{nm} \\
    \end{pmatrix}
    $$

  Start off by making processes that are not part of a deadlocked set. Initially, all processes are unmarked. Then the following steps are performed:

  1. Mark each process that has a row in the allocation matrix $A$ of all zeros. A process that has no allocated resources cannot participate in a deadlock.
  2. Initialize a temporary vector $W$ to equal the availability vector $V$.
  3. LOOP:
     * Find an indes $i$ such that process $i$ is currently unmarked and the $i$th row of $Q$ is less than or equal to $W$. That is, $Q_{ik} \le W_k$, for $1 \le k \le m$. If no such row is found, terminate the algorithm.
     * If such row is found, mark process $i$ and add the corresponding row of the allocation matrix to $W$. That is, set $W_k = W_k + A_{ik}$, for $1 \le k \le m$. Return to Step 3.

  A deadlock exists if and only if there are unmarked processes at the end of the algorithm. The set of unmarked rows corresponds precisely to the set of deadlocked processes.

* The strategy in this algorithm is to find a process whose resource requests can be satisfied with the available resources, then assume those resources are granted and the process runs to completion and releases all of its resources. The algorithm then looks for another process to satisfy.
* This algorithm does not guarantee to prevent deadlock; that will depend on the order in which future requests are granted. All it does is determine if deadlock exists.



## Recovery Strategies

* Once deadlock has been detected, some strategy is needed for recovery. The following are possible approaches, listed in the order of increasing sophistications:

  1. Abort all deadlocked processes. (One of the most common, solutions adopted in operating systems.)

  2. Back up each deadlocked process to some previously defined checkpoint, and restart all processes.

     This requires that rollback and restart mechanisms be built into the system. The risk in this approach is that the original deadlock may recur. However, the nondeterminacy of concurrent processing may ensure that this does not happen.

  3. Successively abort deadlocked processes until deadlock no longer exist.

     The order in which processes are selected for abortion should be on the basis of some criterion of minimum cost. After each abortion, the detection algorithm must be reinvoked to see whether deadlock still exists.

  4. Successively preempt resources until deadlock no longer exists.

     As in (3), a cost-based selection should be used, and re-invocation of the detection algorithm is required after each preemption. A process that has a resource preempted from it must be rolled back to a point prior to its acquisition of that resource.

  For (3), and (4), the process selection criteria could be one of the following.

  * least amount of processor time consumed so far
  * least amount of output produced so far
  * most estimated time remaining
  * least total resources allocated so far
  * lowest priority



## Advantages and Disadvantages of Deadlock Detection Algorithm

### Advantages

* It leads to early detection.
* The algorithm is relatively simple because it is based on incremental changes to the state of the system.

### Disadvantages

* Frequent checks consume considerable processor time.






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
