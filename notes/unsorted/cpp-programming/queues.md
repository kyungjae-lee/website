[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Queues

# Queues



## Header File

```cpp
#include <queue>
```



## Operations

* **Core Interface**

  | Operation | Effect                                                       |
  | --------- | ------------------------------------------------------------ |
  | `push()`  | Inserts an element into the queue                            |
  | `front()` | Returns the next element in the queue without removing it (the element that was inserted first) |
  | `back()`  | Returns the last element in the queue without removing it (the element that was inserted last) |
  | `pop()`   | Removes an element from the stack (does not return it)       |
  | `size()`  | Returns the current number of elements                       |
  | `empty()` | Returns whether the stack is empty (equivalent to `size()==0`) |
  
  > Calling `front()`, `back()`, and `pop()` for an empty queue always results in undefined behavior. The member functions `size()` and `empty()` are provided to check whether the stack contains elements.