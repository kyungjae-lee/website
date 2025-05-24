[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Vector (`std::vector`)

# Vector (`std::vector`)



## Overview

* A **sequence container** that represents a dynamic array capable of changing size.
* Stores elements in **contiguous memory**, allowing access via pointers or `[]` indexing—just like C-style arrays.
* **Efficient for random access** and **adding/removing elements at the end**.
* Safer element access with `vector::at()`, which throws a `std::out_of_range` exception on invalid indices.
* **Inefficient for insertions or deletions at arbitrary positions** due to element shifting.
* Uses a **dynamically allocated array** internally:
  - If capacity is exceeded, it **reallocates and copies** all elements to a new memory block.
  - Frequent reallocations can be costly; prefer reserving capacity ahead of time when possible.
  - Reallocations generally occur at **logarithmically increasing intervals**.

