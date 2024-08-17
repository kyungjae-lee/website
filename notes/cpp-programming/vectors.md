[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Vectors

# Vectors



## Header File

```cpp
#include <vector>
```



## Operations

* **Constructors & Destructors**

  | Operation                   | Effect                                                       |
  | --------------------------- | ------------------------------------------------------------ |
  | `vector<Elem> c`            | Default constructor; creates an empty vector without any elements |
  | `vector<Elem> c(c2)`        | Copy constructor; creates a new vector as a copy of c2 (all elements are copied) |
  | `vector<Elem> c = c2`       | Copy constructor; creates a new vector as a copy of c2 (all elements are copied) |
  | `vector<Elem> c(rv)`        | Move constructor; creates a new vector, taking the contents of the r value rv (since C++11) |
  | `vector<Elem> c = rv`       | Move constructor; creates a new vector, taking the contents of the r value rv (since C++11 |
  | `vector<Elem> c(n)`         | Creates a vector with n elements created by the default constructor |
  | `vector<Elem> c(n, elem)`   | Creates a vector initialized with n copies of element elem   |
  | `vector<Elem> c(beg, end)`  | Creates a vector initialized with the elements of the range [beg, end) |
  | `vector<Elem> c(initlist)`  | Creates a vector initialized with the elements of initializer list initlist (since C++11) |
  | `vector<Elem> c = initlist` | Creates a vector initialized with the elements of initializer list initlist (since C++11) |
  | `c.~vector()`               | Destroys all elements and frees the memory                   |

* **Non-modifying Operations**

  | Operation           | Effect                                                       |
  | ------------------- | ------------------------------------------------------------ |
  | `c.empty()`         | Returns whether the container is empty  (equivalent to size()==0 but might be faster) |
  | `c.size()`          | Returns the current number of elements                       |
  | `c.max_size()`      | Returns the maximum number of elements possible              |
  | `c.capacity()`      | Returns the maximum possible number of elements without reallocation |
  | `c.reserve(num)`    | Enlarges capacity, if not enough yet                         |
  | `c.shrink_to_fit()` | Request to reduce capacity to fit number of elements  (since C++11) |
  | `c1 == c2`          | Returns whether c1 is equal to c2 (calls == for elements)    |
  | `c1 != c2`          | Returns whether c1 is not equal to c2; !(c1==c2)             |
  | `c1 < c2`           | Returns whether c1 is less than c2                           |
  | `c1 > c2`           | Returns whether c1 is greater than c2; c2>c1                 |
  | `c1 <= c2`          | Returns whether c1 is less than or equal to c2; !(c2<c1)     |
  | `c1 >= c2`          | Returns whether c1 is greater than or equal to c2; !(c1<c2)  |

* **Assignments**

  | Operation            | Effect                                                       |
  | -------------------- | ------------------------------------------------------------ |
  | `c = c2`             | Assigns all elements of c2 to c                              |
  | `c = initlist`       | Assigns all elements of the initializer list initlist to c (since C++11) |
  | `c.assign(n, elem)`  | Assigns n copies of element elem                             |
  | `c.assign(beg, end)` | Assigns the elements of the range [beg, end)                 |
  | `c.assign(initlist)` | Assigns all the elements of the initializer list initlist    |
  | `c1.swap(c2)`        | Swaps the data of c1 and c2                                  |
  | `swap(c1, c2)`       | Swaps the data of c1 and c2                                  |

* **Element Access**

  | Operation   | Effect                                                       |
  | ----------- | ------------------------------------------------------------ |
  | `c[idx]`    | Returns the element with index idx (no range checking)       |
  | `c.at(idx)` | Returns the element with index idx (throws range-error  exception if idx is out of range) |
  | `c.front()` | Returns the first element (no check whether a first element exists) |
  | `c.back()`  | Returns the last element (no check whether a last element exists) |

* **Iterator Operations**

  | Operation     | Effect                                                       |
  | ------------- | ------------------------------------------------------------ |
  | `c.begin()`   | Returns a random-access iterator for the first element       |
  | `c.end()`     | Returns a random-access iterator for the position after the last element |
  | `c.cbegin()`  | Returns a constant random-access iterator for the  element (since C++11) |
  | `c.cend()`    | Returns a constant random-access iterator for the position after the last element (since C++11) |
  | `c.rbegin()`  | Returns a reverse iterator for the first element of a reverse iteration |
  | `c.rend()`    | Returns a reverse iterator for the position after the last  element of a reverse iteration |
  | `c.crbegin()` | Returns a constant reverse iterator for the first element of a  reverse iteration (since C++11) |
  | `c.rend()`    | Returns a constant reverse iterator for the position after the last element of a reverse iteration (since C++11) |

* **Insert & Remove**

  | Operation                   | Effect                                                       |
  | --------------------------- | ------------------------------------------------------------ |
  | `c.push_back(elem)`         | Appends a copy of elem at the end                            |
  | `c.pop_back()`              | Removes the last element (does not return it)                |
  | `c.insert(pos, elem)`       | Inserts a copy of elem before iterator position pos and returns the position of the new element |
  | `c.insert(pos, n, elem)`    | Inserts n copies of elem before iterator position pos and returns the position of the first new element (or pos if there is no new element) |
  | `c.insert(pos, beg, end)`   | Inserts a copy of all elements of the range [beg, end) before iterator position pos and returns the position of the first new element (or pos if there is no new element) |
  | `c.insert(pos, initlist)`   | Inserts a copy of all elements of the initializer list  initlist before iterator position pos and returns the position of the first new element (or pos if there is no new element; since C++11) |
  | `c.emplace(pos, args, ...)` | Inserts a copy of an element initialized with args before iterator position pos and returns the position of the new element (since C++11) |
  | `c.emplace_back(args, ...)` | Appends a copy of an element initialized with args at the end (returns nothing; since C++11) |
  | `c.erase(pos)`              | Removes the element at iterator position pos and returns the position of the next element |
  | `c.erase(beg, end)`         | Removes elements of the range [beg, end) and returns the of the next element |
  | `c.resize(num)`             | Changes the number of elements to num (if size() grows new elements are created by their default constructor) |
  | `c.resize(num, elem)`       | Changes the number of elements to num (if size() grows new elements are copies of elem) |
  | `c.clear()`                 | Removes all elements (empties the container)                 |