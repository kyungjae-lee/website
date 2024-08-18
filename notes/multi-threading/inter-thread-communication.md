[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Multi-Threading (POSIX Threads)</a> > Inter-Thread Communication

# Inter-Thread Communication



## Introduction to Inter-Thread Communication

A big software system may be a multi-threaded software where multiple modules are running as independent threads of the same process and the threads may need to exchange data with one another. In this case, we need to set up communication between threads (i.e., exchange of data). 

**Inter-Process Communication (IPC)**, the techniques used to set up the data exchange between processes, can also be used for the communication between threads. The followings are the tools used to set up the IPC:

* Sockets
* Message queues
* Pipes
* Shared Memory

However, for **Inter-Thread Communication**, using **callbacks** (or **function pointers**) is preferred to using IPC techniques. This is because callbacks are:

* Very fast
* No actual transfer of data, but only the transfer of computation (e.g., using function pointers)
* No special attention required from the kernel or the OS because they are nothing but the user-space function calls. 
  * Everything is done in the user-space and hence, no kernel resource is required to set up the communication.



## Transfer of Data vs. Transfer of Computation

Let's say there are two entities (i.e., processes or threads) that may or may not be running on the same machine:

* Entity 1: Owns two integers `a` and `b` (Data)
* Entity 2: Owns the function `multiply(a, b) { c = a * b }` (Computation), also owns a local storage where the multiplication result `c` will be stored

There are two possible scenarios where Entity 1 can compute `a * b`:

* **Transfer of data**

  Entity 1 transfers the data `a` and `b` to Entity 2, Entity 2 uses those data to compute `a * b`, and stores the result into its local storage. In this case, computation is stationary on Entity 2.

  Transfer of data can be used in general regardless of whether Entity 1 and 2 are sharing the same Virtual Address Space or not.

* **Transfer of computation** 

  Entity 2 transfers the function (computation) to Entity 1, Entity 1 uses that function to compute `a * b`, and directly stores the result into the local storage owned by Entity 2. Transfer of computation is nothing but a function call using the function pointer.

  Transfer of computation is feasible only when Entity 1 and 2 are sharing the same Virtual Address Space where they both have access to each other's local storage.

In both cases, the resultant value will be saved only on Entity 2's machine.



## Example - Transfer of Computation

The following is a simple example that demonstrates the transfer of computation.

```c
/* Entity 1 (Thread 1) - Owner of data */
int a;
int b;

int (*fn_ptr)(int a, int b) = NULL;

void multiply(a, b)
{
    if (fn_ptr)
    {
        fn_ptr(a, b);
    }
}

void data_gen()
{
    a = 10;
    b = 10;
}

void compute()
{
    data_gen();
    multiply(a, b);
}
```

```c
/* Entity 2 (Thread 2) - Owner of computation */
extern int (*fn_ptr)(int a, int b);

int c;

void multiply(a, b)
{
    c = a * b;
}

void callback_registration()
{
    fn_ptr = multiply;
}
```

> Step 1: Thread 2 registers the `multiply()` function to the Thread 1's function pointer `fn_ptr`.
>
> Step 2: Thread 1 generates the data and performs the multiplication using the function registered to `fn_ptr`.

Above example shows important properties of this model:

* Data transfer or copy of data from one memory location to another does not take place
* Usually a uni-directional communication
* Computation logic is owned by one entity, but is executed by another.



## Publisher Subscriber Model (a.k.a. Notification Chain)

Transfer of Computation (TOC) leads to a famous architectural communication mode called the **Publisher Subscriber Model (a.k.a. Notification Chain)**:

* The pattern of communication which is based on transfer of communication
* The thread which generates the data is called the **Publisher**.
* The thread which owns the data processing function is called the **Subscriber**.

* The activity of TOC is called the **Callback Registration**.
* The activity of invoking the function through the function pointer is called the **Notification**.

 This model is widely used to set up the Inter-Thread Communication.





## References

Sagar, A. (2022). *Part A - Multithreading & Thread Synchronization - Pthreads* [Video file]. Retrieved from  https://www.udemy.com/course/multithreading_parta/
