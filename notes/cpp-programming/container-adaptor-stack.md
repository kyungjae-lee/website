[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Container Adaptor - `std::stack`

# Container Adaptor - `std::stack`



## `std::stack`

* Declared in the `<stack>` header file.
* Last-In, First-Out (LIFO) data structure.
* Implemented as an **adaptor** over other STL containers.
  * Can use `vector`, `list`, or `deque` as the underlying container.
  * Stack methods work through delegation—they internally call the `back()`, `push_back()`, and `pop_back()` functions of the underlying container.

* All operations occur at one end of the stack (i.e., the **top**).
* Iterators are **not supported**.
  * This makes sense, as stacks only allow insertions and deletions at one end.
* Use cases:
  * Function call stack
  * Expression evaluation & parsing in compilers, calculators and so on
  * Backtracking algorithms (e.g., Maze solvers, Sudoku solvers, Depth-First Search (DFS) in graphs)
  * Syntax checking (e.g., Code parsers, interpreters, linters, balanced parentheses)



### Initialization

Because `std::stack` is a container adaptor, you have the flexibility to specify the underlying container—such as `deque`, `vector`, or `list`—at the time of stack creation.

```cpp
std::stack<int> s1;						// deque (by default)
std::stack<int, std::vector<int>> s2;	// vector
std::stack<int, std::list<int>> s3;		// list
std::stack<int, std::deque<int>> s4;	// deque
```

### Stack Operations

For more information, see [cppreference.com](https://en.cppreference.com/w/).

| Operation | Behavior                                     |
| --------- | -------------------------------------------- |
| `push()`  | Insert an element at the top of the stack.   |
| `pop()`   | Remove an element from the top of the stack. |
| `top()`   | Access the top element of the stack.         |
| `empty()` | Is the stack empty?                          |
| `size()`  | Number of elements in the stack.             |



## Project: Usage of `std::stack`

It's best to adhere to the fundamental operations of a stack. Introducing additional methods like `insert()` compromises the integrity of the stack's intended behavior.

```cpp
#include <iostream>
#include <stack>
#include <vector>
#include <list>

// Prints the stack by repeatedly topping and popping the elements.
// Note that this function is passed a stack 'by value' so whatever happens
// inside this function will not affect the original stack.
template <typename T>
void print(std::stack<T> s)
{
    std::cout << "[ ";
    while (!s.empty())
    {
        T elem = s.top();
        s.pop();
        std::cout << elem << " ";
    }
    std::cout << "]" << std::endl;
}

// push(), pop(), empty(), size()
void test(void)
{
    std::cout << "\nTEST" << std::endl;

    std::stack<int> s;
    std::stack<int, std::vector<int>> s1;
    std::stack<int, std::list<int>> s2;
    std::stack<int, std::deque<int>> s3;

    for (int i : {1, 2, 3, 4, 5})
    {
        s.push(i);
    }
    print(s);

    s.push(100);
    print(s);

    s.pop();
    s.pop();
    print(s);

    while (!s.empty())
    {
        s.pop();
    }
    print(s);

    std::cout << "Size: " << s.size() << std::endl;

    s.push(10);
    print(s);

    s.top() = 100;
    print(s);
}

int main(int argc, char *argv[])
{
    test();
    return 0;
}
```

```plain

TEST
[ 5 4 3 2 1 ]
[ 100 5 4 3 2 1 ]
[ 4 3 2 1 ]
[ ]
Size: 0
[ 10 ]
[ 100 ]
```
