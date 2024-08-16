[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > RAID Storage

# RAID Storage



## What is RAID?

* Commonly stands for **Redundant Array of Inexpensive Disks** (or **Redundant Array of Independent Drives**)
* A RAID puts together two or more separate drives that appear to behave as a single drive.
* A RAID distributes data across volumes.



## Why RAID?

* There are several forms of RAID that are designed to:
  * Provide faster access to data
  * Keep data safe
  * Or a combination of both



## Common Implemented RAID Levels

* **RAID 0**
  * Blocks are striped, no mirror, no parity
  * Fastest
  * No protection from disk failure
  * Requires 2 or more disks
* **RAID 1**
  * Blocks mirrored, no stripe, no parity
  * Safest
  * Most disk failure protection
  * Best for critical data where access speed is not an issue
  * Requires 2 or more disks

* **RAID 4**
  * Blocks are striped, dedicated parity
  * Fast and safe
  * Best for general use on SSDs
  * Fast read/write of large files
  * Requires 3 or more disks
* **RAID 5**
  * Blocks stribed, distributed parity
  * Best for general use on HDDs
  * Fast read/write use on HDDs
  * Fast read/write of large files
  * Requires 3 or more disks
* **RAID 6**
  * Similar to RAID 5 with extra parity recovery information
  * Allows for the failure of 2 disks
  * Slightly slower writes
  * Requires 4 or more disks
* **RAID 1+0 (10)**
  * Blocks mirrored and striped
  * Fast and safe
  * Requires 4 or more disks



## Stripted Volume

* A striped volume spreads data from a single volume over two or more drives.
* Each drive contains only a portion of a file.
* When reading or writing, the request gets sent to all drives at the same time and each drive works only on its portion of the file.
* This system FAILS if one drive fails!



## Mirrored Volume

* Mirrored volumes each contain a copy of the entire drive.
* Provides protection of data through reducndancy.
* If one drive fails, the other(s) still have a copy of the data.



## Parity

* Creates Fault Tolerance in striped drives.
* Calculates info about data stored in two drives and stores on a third.
* If a drive fails, then the data can be rebuilt for it from the parity blocks.
* Safer, but slower and requires more storage.
* Distributed parity distributes parity data across all drives in the array.





## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.

