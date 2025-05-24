[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Introduction to STL Containers

# Introduction to STL Containers



## Containers

* Data structures that can store object of *almost* any type. (Template-based classes)
* Each container has member functions. (Some are specific to the container, others are available to all containers.)
* Each container has an associated header file. `#include <container_type>` (e.g., `#include <stack>`)



## Common Methods of Containers

| Function                      | Description                                                  |
| ----------------------------- | ------------------------------------------------------------ |
| Default constructor           | Initializes an empty container                               |
| Overloaded constructors       | Initializes containers with many options                     |
| Copy constructor              | Initializes a container as a copy of another container       |
| Move constructor              | Moves existing container to new container                    |
| Destructor                    | Destroys a container                                         |
| Copy assignment (`operator=`) | Copy one container to another                                |
| Move assignment (`operator=`) | Move one container to another                                |
| `size`                        | Returns the number of elements in the container              |
| `empty`                       | Returns boolean - is the container empty?                    |
| `insert`                      | Insert an element into the container                         |
| `operator<` and `operator<=`  | Returns boolean - compare contents of 2 containers           |
| `operator>` and `operator>=`  | Returns boolean - compare contents of 2 containers           |
| `operator==` and `operator!=` | Returns boolean - are the contents of 2 containers equal or not |
| `swap`                        | Swap the elements of 2 containers                            |
| `erase`                       | Remove element(s) from a container                           |
| `clear`                       | Remove all elements from a container                         |
| `begin` and `end`             | Returns iterators to first element or end                    |
| `rbegin` and `rend`           | Returns reverse iterators to first element or end            |
| `cbegin` and `cend`           | Returns constant iterators to first element or end           |
| `crbegin` and `crend`         | Returns constant reverse iterators to first element or end   |



## Types of Elements Containers Can Store

To ensure your custom objects work well with the STL, they should meet the following requirements:

* A **copy** of the element is stored in the container.
  - All primitive types (e.g., `int`, `char`, `double`) are fine.
* For non-primitive types, elements should be:
  - Copyable and assignable (i.e., have a copy constructor and copy assignment operator).
  - Movable (i.e., have a move constructor and move assignment operator) for better performance.
* Ordered associative containers (e.g., `std::map`, `std::set`) require elements to be comparable:
  - They use `operator<` and `operator==`, so your element type must support these operators.
