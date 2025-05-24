[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Introduction to Standard Template Library

# Introduction to Standard Template Library



## Prerequisites

* g++ (C++17)
* Vim
* make
* GDB
* Valgrind



## What is STL?

The Standard Template Library (STL) is a collection of template classes that provide commonly used data structures and algorithms. STL consists of four main components:

* Algorithms
* Containers
* Functions (Functors)
* Utilities

### Algorithms

STL defines a collection of standalone functions that operate on ranges of elements using iterators. These functions perform a variety of tasks, including:

* Non-modifying - Search, compare, count
* Modifying - Copy, move, replace, fill, partition, sort, shuffle

Having a good knowledge of the standard library will accelerate your programming and help you avoid reinventing solutions to low-level problems.

### Containers

The bread and butter of STL containers is what most developers think of when they refer to the C++ Standard Library. STL offers several categories of containers, each serving different use cases:

* Sequence Containers
  * Store sequences of homogeneous elements accessed by index.
  * e.g., Vector, list, deque
* Associative Containers
  * Store data as key-value pairs.
  * All keys share the same type, and all values share the same type; however, key and value types can differ.
  * Elements are stored and accessed by their keys instead of indices.
  * e.g., Sets, maps
* Adapter Containers
  * Provide a modified interface over existing containers, often with restricted functionality. For example, a stack may use a deque internally but exposes a LIFO interface only.
  * e.g., Queue, stack

### Functions (Functors)

STL provides **function objects**, also known as **functors**, which behave like regular functions. A functor is implemented by defining a class that overloads the `operator()`.

Some common types of functionality provided by standard functors include:

- Arithmetic (e.g., `std::plus`, `std::minus`)
- Comparison (e.g., `std::greater`, `std::less`)
- Logical (e.g., `std::logical_and`, `std::logical_not`)

These functors can be used anywhere a callable is expected, such as in algorithms that accept predicates.

### Utilities

Especially with the additions introduced in C++11 and beyond, the STL includes several headers that don't neatly fit into a specific category but are best considered utilities. These provide powerful tools to support modern C++ programming:

- `<thread>` – Facilities for creating and managing multi-threaded programs.
- `<iterator>` – Utilities for working with iterators, offering a universal way to access container elements.
- `<chrono>` – A standard way to represent, manipulate, and convert between time durations and points in time.
- `<any>` – Allows storage of values of any type, enabling the creation of heterogeneous containers.
- `<memory>` – Smart pointers and memory management utilities that help prevent resource leaks and manage ownership safely.



## Understanding C++ Templates

To understand the C++ Standard Template Library (STL), it's essential to first grasp **C++ templates** and their capabilities. Templates allow programmers to write **generic code** that can operate on a wide variety of data types without duplication.

### Example - Templated Class

```cpp
#include <iostream>
#include <string>

template<typename T>
class container
{
public:
    explicit container(T t) : t(t) {}
    friend std::ostream& operator<<(std::ostream &os, const Container<T> &c)
    { return (os << "Container holding: " << c.t); }

private:
    T t;
};

int main(int argc, char *argv[])
{
    container<int> c(100);
    container<std::string> s("Some String");

    std::cout << c << std::endl;
    std::cout << s << std::endl;

    return 0;
}
```

```plain
Container holding: 100
Container holding: Some String
```

### Example - Templated Function

```cpp
#include <iostream>

// Normal templated function
template<typename T>
bool is_greater(T t1, T t2)
{
    return t1 > t2;
}

// Special templated function for <int> type
template<>
bool is_greater(int t1, int t2)
{
    std::cout << "Specialized for int" << std::endl;
    return t1 > t2;
}

int main(int argc, char *argv[])
{
    std::cout << std::boolalpha; // Print true or false instead of 1 or 0

    std::cout << is_greater(5, 10) << std::endl; // Spacial templated function
    std::cout << is_greater(23.45, 10.789) << std::endl;
    std::cout << is_greater("ZZZ", "AAA") << std::endl;
    std::cout << is_greater('c', 'r') << std::endl;

    return 0;
}
```

```plain
false
true
true
false
```

### Example - Templated Lambda

```cpp
int main(int argc, char *argv[])
{
    std::cout << std::boolalpha;
    
    auto f = [] <typename T> (T t1, T t2)
	{
    	return t1 == t2;
	};
    
    std::cout << "Lambda returns: " << f(10, 10) << std::endl;
    
    return 0;
}
```

```plain
true
```
