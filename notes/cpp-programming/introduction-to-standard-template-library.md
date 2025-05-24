[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Introduction to Standard Template Library

# Introduction to Standard Template Library



## What is STL?

* A library of powerful, reusable, adaptable, generic classes and functions.
* Implemented using C++ templates.
* Implements common data structures and algorithms.
* Huge class library!
* Developed by Alexander Stepanov (1994).



## Why Use STL?

* Assortment of commonly used containers.
* Known time and size complexity.
* Tried and tested - Reusability!
* Consistent, fast, and type-safe.
* Extensible



## Elements of the STL

* **Containers** - Collections of objects or primitive types (array, vector, deque, stack, set, map, etc.)
* **Algorithms** - Functions for processing sequences of elements from containers (find, max, count, accumulate, sort, etc.) The STL has about 60 algorithms that can be used and extended to work with any type of data.
* **Iterators** - Generate sequences of element from containers (forward, reverse, by value, by reference, constant, etc.)

### Example

```cpp
#include <iostream>
#include <vector>       
#include <algorithm>    // std::sort, std::reverse
#include <numeric>      // std::accumulate

int main(int argc, char *argv[])
{
    std::vector<int> v {1, 5, 3};

    // Sort a vector
    std::sort(v.begin(), v.end());
    for (auto elem : v)
        std::cout << elem << " ";
    std::cout << std::endl;
    
    // Reverse a vector
    std::reverse(v.begin(), v.end());
    for (auto elem : v)
        std::cout << elem << " ";
    std::cout << std::endl;

    // Accumulate
    int sum{};
    sum = std::accumulate(v.begin(), v.end(), 0); // 3rd param: initial running sum
    std::cout << sum << std::endl;
    
    return 0;
}
```

```plain
1 3 5
5 3 1
9
```



## Types of Containers

* **Sequence containers** - array, vector, list, forward_list, deque
  * Maintains the ordering of the inserted elements.
* **Associative containers** - set, multiset, map, multimap
  * Inserts elements in a pre-defined order, or no order at all.
* **Container adapters** - stack, queue, priority queue
  * Wrappers around existing containers that provide a different interface. Not standalone container implementations but instead reuse existing containers (such as `deque` or `vector`) under the hood while exposing a modified API.
  * Does not support iterators, can't be used with STL algorithms.



## Types of Iterators

* **Input iterators** - From the container to the program.
* **Output iterators** - From the program to the container.
* **Forward iterators** - Navigate one item at a time in one direction.
* **Bi-directional iterators** - Navigate one item at a time both directions.
* **Random access iterators** - Directly access a container item.  (Can use the subscript operator to directly access elements.)



## Types of Algorithms

There are about 60 algorithms in the STL. Depending on whether an algorithm modifies the sequence it operates on, they can be classified into two groups:

* Non-modifying
* Modifying
