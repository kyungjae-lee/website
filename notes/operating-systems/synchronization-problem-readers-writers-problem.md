[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Synchronization Problem - Readers/Writers Problem

# Synchronization Problem - Readers/Writers Problem



## Introduction

* In dealing with the design synchronization and concurrency mechanisms, it is useful to be able to relate the problem at hand to known problems, and to be able to test any solution in terms of its ability to solve these  known problems.
* One of the several problems that appear frequently is the **readers/writers problem**. (Another is, producer/consumer problem)



## Readers/Writers Problem

* **Definition**

  There is a data area shared among a number of processes. The data area could be a file, a block of main memory, or even a bank of processor registers. There are a number of processes that only read the data area (readers) and a number that only write to the data area (writers). The condition that must be satisfied are as follows:

  1. Any number of readers may simultaneously read the file.
  2. Only one writer at a time may write to the file.
  3. If a writer is writing to the file, no reader may read it.

* Different variations of the problem include:

  * Readers have priority (can starve writers)
  * Writers have priority
  * Alternate priorities (i.e., don't allow too many new readers to enter if a writer is waiting, etc.)



## Solutions to the Readers/Writers Problem Using Semaphore

* Readers Have Priority

  ```c
  /* A solution to the Readers/Writers Problem using semaphore - Readers have priority */
  
  int readcount;
  semaphore x = 1, wsem = 1;
  
  void reader()
  {
      while (true)
      {
          semWait(x);
          readcount++;
          if (readcount == 1)
              semWait(wsem);
          semSignal(x);
          READUNIT();
          semWait(x);
          readcount--;
          if (readcount == 0)
              semSignal(wsem);
          semSignal(x);
      }
  }
  
  void writer()
  {
      while (true)
      {
          semWait(wsem);
          WRITEUNIT();
          semSignal(wsem);
      }
  }
  
  void main()
  {
      readcount = 0;
      parbegin(reader, writer);
  }
  ```

* Writers Have Priority

  ```c
  /* A solution to the Readers/Writers Problem using semaphore - Readers have priority */
  
  int readcount, writecount;
  semaphore x = 1, y = 1, z = 1, wsem = 1, rsem = 1;
  
  void reader()
  {
      while (true)
      {
          semWait(z);
          semWait(rsem);
          semWait(x);
          readcount++;
          if (readcount == 1)
              semWait(wsem);
          semSignal(x);
          semSignal(rsem);
          semSignal(z);
          READUNIT();
          semWait(x);
          readcount--;
          if (readcount == 0)
              semSignal(wsem);
          semSignal(x);
      }
  }
  
  void writer()
  {
      while (true)
      {
          semWait(y);
          writecount++;
          if (writecount == 1)
              semWait(rsem);
          semSignal(y);
          semWait(wsem);
          WRITEUNIT();
          semSignal(wsem);
          semWait(y);
          writecount--;
          if (writecount == 0)
              semSignal(rsem);
          semSignal(y);
      }
  }
  
  void main()
  {
      readcount = writecount = 0;
      parbegin (reader, writer);
  }
  ```

  > The semaphore `rsem` inhibits all readers while there is at least one writer desiring access to the data area.
  >
  > The variable `writecounter` controls the setting of `rsem`.
  >
  > The semaphore `y` controls the updating of `writecount`.






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.

