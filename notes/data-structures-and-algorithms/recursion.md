[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Recursion

# Recursion



## Introduction to Recursion

* Recursion is a programming or mathematical concept where a function or algorithm calls itself (until it doesn't) in order to solve a problem by breaking it down into smaller instances of the same problem.

* Key components of recursion:

  * **Base case**

    The terminating condition that defines when the recursion should stop. It's the simplest case that can be solved directly without further recursive calls.

    If the base case is not defined correctly, this function call can result in an infinite loop, continually adding new instances of the function to the call stack. This can eventually lead to a "stack overflow" error.

  * **Recursive case**

    The part where the problem is divided into smaller instances of the same problem. The function calls itself with modified inputs to solve these  smaller subproblems.

### Example: Factorial

```cpp
#include <iostream>

using namespace std;

int factorial(int n)
{
	// Base case: factorial of 0 or 1 is 1
	if (n == 0 || n == 1) return 1;
	// Recursive case: n! = n * (n-1)!
    else return n * factorial(n - 1);
}

int main(void)
{
    cout << factorial(4);
}
```

```plain
24
```



## Call Stack

* The call stack is a data structure used by programs to manage function calls and track their execution order. It stores information about the  active functions, their local variables, and the point to which the program should return after each function completes.
* When a function is called the function gets pushed onto the call stack, and when it returns it gets popped from the call stack.

