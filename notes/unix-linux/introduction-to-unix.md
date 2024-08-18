[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > Introduction to UNIX

# Introduction to UNIX



## The Operating System

* **Operating System**
  - The software that manages the computer's hardware and provides a convenient and safe environment for running programs.
  - Acts as an interface between programs and the hardware resources that these programs access (like memory, hard disk and printer).
  - Loaded into memory when a computer is booted and remains active as long as the machine is up.
* Some common actions of operating system in most systems:
  - Allocates memory for the program and loads the program to the allocated memory.
  - Loads the CPU registers with control information related to the program. The registers maintain the memory locations where each segment of a program is stored.
  - The instructions provided in the program are executed by the CPU. The operating system keeps track of the instruction that was last executed. This enables it to resume a program if it had to be taken out of the CPU before it completed execution.
  - If the program needs to access the hardware, it makes a call to the operating system rather than attempting to do the job itself. (i.e, If the program needs to read a file on disk, the operating system directs the disk controller to open the file and make the data available to the program.)
  - After the program has completed execution, the operating system cleans up the memory and registers and makes them available for the next program.
* **Process**
  - A program in execution (running program)
* I/O operations (e.g, accessing the disk or the terminal to read or write data) keep the CPU idle, so the operating system takes the program out of the CPU while the I/O operation is in progress. It then schedules another program
  to run.
    - The previous program can resume execution only after the I/O operation completes, maximizing utilization of the CPU.
* **Multiprogramming** systems
  - Systems that allow multiple programs to reside in memory.
  - In a multiprogramming environment, the operating system has to ensure that a process performing an I/O operation doesn't hold up the CPU. It must schedule  another process while the I/O operation is in progress. The previous process is said to *block*, e.g., wait for the event to complete.




## The UNIX Operating System

* **UNIX**
  - A family of **multitasking**, **multiuser** computer operating systems. 
    - Multitasking: The concurrent execution of multiple tasks (a.k.a. processes) over a certain period of time.
  - Derived from the original *AT&T* UNIX, whose development started in 1969 at the *Bell Labs* research center by *Ken Thompson*, *Dennis Ritchie*, and others.




## Knowing Your Machine

* **Terminal emulation facility** enables a user to run a program on a remote machine using remote machine's memory and CPU, and not the user's own.
    - The user's machine need only to provide the minimal resources required by the terminal emulation software.




## System Administrator

* A **system administrator** or **admin** is a person who is responsible for the upkeep, configuration, and reliable operation of computer systems, especially multi-user computers, such as servers.
* The *administrator* uses a special user-id called **root** to log on to the system which has near-absolute powers.



## Linux and GNU

* **GNU** ("GNU's Not UNIX!")
  - Free Software Foundation that provides many of the important Linux tools.
* **GNU General Public License**  
  - Mandates that developers and sellers distributing products under the GNU.
  - General Public License make the source code public.
