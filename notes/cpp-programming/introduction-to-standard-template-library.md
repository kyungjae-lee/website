[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Introduction to Standard Template Library

# Introduction to Standard Template Library



## Overview

* What is the STL
* Generic programming / Meta-programming
  * Preprocessor macros
  * Function templates
  * Class templates

* STL containers
  * Array
  * Vector
  * Deque
  * List and forward list
  * Set and multi set
  * Map and multi map
  * Stack and queue
  * Priority queue
  * Algorithms

* STL iterators
* STL algorithms



## What is STL?

* A library of powerful, reusable, adaptable, generic classes and functions
* Implemented using C++ templates
* Implements common data structures and algorithms
* Huge class library!!!
* Alexander Stepanov (1994)



## Why use the STL?

* Assortment of commonly used containers
* Known time and space complexity
* Tried and tested - Reusability!!!
* Consistent, fast, and type-safe
* Extensible



## Elements of the STL

### Containers

* Collections of objects or primitive types
* Types of containers
  * Sequence containers - array, vector, list, forward_list, deque
  * Associative containers - set, multi set, map, multi map
  * Container adapters - stack, queue, priority queue

### Algorithms

* Functions for processing sequences of elements from containers
* About 60 algorithms in the STL
* Non-modifying
* Modifying
* e.g., find, max, count, accumulate, sort, etc.

### Iterators

* Generate sequences of element from containers
* Types of iterators
  * Input iterators - from the container to the program
  * Output iterators - from the program to the container
  * Forward iterators - navigate one item at a time in one direction
  * Bi-directional iterators - navigate one item at a time both directions
  * Random access iterators - directly access a container item
* e.g., forward, reverse, by value, by reference, constant, etc.

### Example

* Vector and sort

  ```cpp
  #include <vector>
  #include <algorithm>
  
  std::vector<int> v{1, 5, 3};
  
  std::sort(v.begin(), v.end());	// Now v contains {1, 3, 5}
  ```

* Accumulate

  ```cpp
  int sum{};
  
  sum = std::accumulate(v.begin(), v.end(), 0);
  std::cout << sum << std::endl;	// 9 (1 + 3 + 5)
  ```
