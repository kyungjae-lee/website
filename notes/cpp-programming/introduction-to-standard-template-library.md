[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Introduction to Standard Template Library

# Introduction to Standard Template Library



## Prerequisites

* g++ (C++17)
* Vim
* make
* GDB
* Valgrind



## What is STL?

STL or the Standard Template Library, is a set of template classes to provide common programming data structures and algorithms. STL consists of four main groups:

* Algorithms
* Containers
* Functions (Functors)
* Utilities

### Algorithms

STL defines a collection of standalone functions that act on ranges of elements (iterators). They do various types of tasks:

* Non-modifying - Search, compare, count
* Modifying - Copy, move, replace, fill, partition, sort, shuffle

Having a good knowledge of what is available from the standard library will accelerate your programming and help you avoid re-solving low-level problems.

### Containers

The bread and butter of STL containers is hat most developers think of when they think of the C++ standard library. STL provides different categories of containers:

* Sequence containers
  * Contains sequences of homogeneous items that are accessed via indices.
  * e.g., Vector, list, deque
* Associative containers
  * Contains data in key-value pairs. 
  * All keys have the same data type, and all values have the same data type, but the key and the value types can be different.
  * Instead of items being stored and accessed by their index, they are stored and associated with their key.
  * e.g., Sets, maps
* Adapter containers
  * These are simply a different interface to an existing data type. For example, a stack might just be a deque on the backend with a more restricted API.
  * e.g., Queue, stack

### Functions (Functors)

STL provides function objects called functors that can be used just like functions. They achieve this by creating a class with an operator overload of the `operator()`. Some functionality they provide are:

* Arithmetic
* Comparison
* Logical

These can be used anywhere a callable is expected.

### Utilities

Especially with the additions after C++11, there are several STL headers that don't really fall under a category and can be considered as utilities:

* Threads - Facilities for creating multi-threaded programs.
* Iterators - Universal ways to access elements in containers.
* Chrono (time) - Standard way to define and convert between times.
* Any - Allows you to store any value into a container so they can become heterogeneous containers.

* Memory - A way to avoid resource-owning pointers which removes the possibility of memory leaks from program.



## Understanding C++ Templates

To understand the C++ STL, you must first understand C++ templates and what they offer programmers. Templates allow programmers to write generic C++ code that can apply to many different types of data.

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



