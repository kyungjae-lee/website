[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Operating Systems</a> > Segmentation

# Segmentation



## Segmentation

* A user program (code + data) can be subdivided into **unequal-sized segments**.

  * Segments may vary in length.
  * There is a maximum segment length.

  Segmentation is similar to dynamic partitioning but is different in that a program may occupy more than one partition and these partitions need NOT be contiguous.

* Logical address = (Segment number, Offset)

* Eliminates internal fragmentation

* It suffers from external fragmentation, but because a process is broken up into multiple segments, the external fragmentation should be less than that of dynamic partition's.

* Unlike paging, segmentation is usually **visible to the programmer** and is provided as a convenience for organizing programs and data. Typically the programmer will assign programs and data to different segments.

* For purposes of modular programming the program or data may be further broken down into multiple segments. The principal inconvenience of this service is that the programmer must be aware of the maximum segment size limitation.

* Along with the segment tables, the OS also maintains a list of all the unoccupied blocks in main memory that are available for segments.

### Address Translation & Segment Tables

* Another consequence of unequal size segments is that there is no simple relationship between logical addresses and physical addresses.
* A segment table entry consists of
  * The **starting address** in main memory of the corresponding segment
  * The **length of the segment** to assure that invalid addresses are not used
* When a process enters the Running state, the address of its segment table is loaded into a special register used by the memory management hardware.
* Address translation sequence:
  1. Extract the segment number as the leftmost $n$ bits of the logical address.
  2. Use the segment number as an index into the process segment table to find the starting physical address of the segment.
  3. Compare the offset, expressed in the rightmost m bits, to the length of the segment. If the offset is greater than or equal to the length, the address is invalid.
  4. The desired physical address is the sum of the starting physical address of the segment plus the offset.



<img src="./img/logical-addresses.png" alt="logical-addresses" width="700">





<img src="./img/examples-of-logical-to-physical-address-translation.png" alt="example-of-logical-to-physical-address-translation" width="650">







## References

Stallings, W. (2018). *Operating Systems: Internals and Design Principles* (9th ed.). Pearson Education, Inc.

