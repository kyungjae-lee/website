[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Summary of Memory Management Techniques

# Summary of Memory Management Techniques



## Memory Management Techniques

| Technique                   | Description                                                  | Strengths                                                    | Weaknesses                                                   |
| --------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fixed Partitioning          | Main memory is divided into a number of static partitions as a system generation time. A process may be loaded into a partition of equal or greater size. | Simple to implement; little OS overhead.                     | Inefficient use of memory due to internal fragmentation; maximum number of active processes is fixed. |
| Dynamic Partitioning        | Partitions are created dynamically, so each process is loaded into a partition of exactly the same size as that process. | No internal fragmentation; more efficient use of main memory. | Inefficient use of processor due to the need for compaction to counter external fragmentation. |
| Simple Paging               | Main memory is divided into a number of equal size frames. Each process is divided into a number of equal-size pages of the same length as frames. A process is loaded by loading all of its pages into available, not necessarily contiguous, frames. | No external fragmentation.                                   | A small amount of internal fragmentation.                    |
| Simple Segmentation         | Each process is divided into a number of segments. A process is loaded by loading all of its segments into dynamic partitions that need not be contiguous. | No internal fragmentation; improved memory utilization and reduced overhead compared to dynamic partitioning. | External fragmentation.                                      |
| Virtual Memory Paging       | As with simple paging, except that it is not necessary to load all of the pages of a process. Nonresident pages that are needed are automatically brought in later. | No external fragmentation; higher degree of multiprogramming; large virtual address space. | Overhead of complex memory management.                       |
| Virtual Memory Segmentation | As with simple segmentation, except that it is not necessary to load all of the segments of a process. Nonresident segments that are needed are automatically brought in later. | No internal fragmentation, higher degree of multiprogramming; large virtual address space; protection and sharing support. | Overhead of complex memory management.                       |






## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.
