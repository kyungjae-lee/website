[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Stacks

# Stacks



## Header File

```cpp
#include <stack>
```



## Operations

* **Core Interface**

  | Operation | Effect                                                       |
  | --------- | ------------------------------------------------------------ |
  | `push()`  | Inserts an element into the stack                            |
  | `top()`   | Returns the next element in the stack (does not remove it)   |
  | `pop()`   | Removes an element from the stack (does not return it)       |
  | `size()`  | Returns the current number of elements                       |
  | `empty()` | Returns whether the stack is empty (equivalent to `size()==0`) |

  > Calling `top()` and `pop()` for an empty stack always results in undefined behavior. The member functions `size()` and `empty()` are provided to check whether the stack contains elements.