[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > Features of UNIX

# Features of UNIX



## Multiuser System

* **Multiprogramming** system
  - UNIX permits multiple programs (that can be run by different users) to remain in memory and compete for the attention of the CPU.      
* **Multiuser** system
  - UNIX allows multiple users to access the single system with one operating system (UNIX) on it.
  - Use `who` command to view the users working on the system.
* **Time Sharing**
  - Method of operation in which multiple users with different programs interact nearly simultaneously with the CPU.
  - Kernel uses the principle of time-sharing for cycling through multiple jobs.
      - It breaks up a unit of time into several *slices*, and a user's job runs for the duration of a *slice*.
      - The moment the allocated time expires, the previous job is kept in abeyance and the next job is taken up.
      - This process goes on until the clock has turned full circle and the first job is taken up once again.
      - This switching happens so quick that every user feels as if he/she is the only one using the system.
  - Even though a program may run for the duration of the time slice allocated for it, it may prematurely leave the CPU during a *blocking* operation (like reading a file) that keeps the CPU idle.



## Multitasking System

* **Multitasking** system

  - UNIX allows a single user run multiple tasks concurrently (switching between tasks very rapidly).
  - A user sees one job running in the *foreground*; the rest run in the *background*.
  - A user can switch jobs between background and foreground, suspend, or even terminate them.

  Machines with multiple CPUs can even dedicate an entire processor for a single program (in a single-user, single-tasking situation).



## Repository of Applications

* UNIX applications:
  - General-purpose tools
  - Text manipulation utilities (called *filters*)
  - Compilers and interpreters
  - Networked applications
  - System administration tools
  - and many more ...
* The *shell* and a set of essential *applications* form part of the POSIX specification.



## The Building-Block Approach in Design

* "Small is beautiful."
  - UNIX keeps small commands for every kind of work, each of which performs one simple job only.
  - UNIX lets the user develop complex command routines by connecting these small commands.
      - *Pipe* (|) allows the output of one command to serve as input to another, thus connecting more than 2 commands.
      - These types of connected commands that can filter/manipulate data in other ways are called *filters*.
* Pattern Matching
  - UNIX features very sophisticated pattern mathing features.
  - `*`(known as a metacharacter) is used by the shell to match a number of characters.
  - Some advanced tools like `grep`, `sed` and `awk` also use a different matecharacter set for matching strings contained in files.
  - e.g., `printf.*name` matches all lines that contain both `printf` and `name`. (This pattern is called a *regular expression*.)



## Programming Facility

* **Shell** is also a programming language designed for programmers.
  - It has all the necessary elements such as control structures, loops, variables, etc.
* **Shell scripts** are programs that run UNIX commands in a batch.
  - Many system's functions can be controlled and automated by using these shell scripts.



## Documentation

* **man** command (program)
  - The help facility retains the most important reference for commands and their configuration files.
