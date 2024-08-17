[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > References

# References



## Overview

* What is a reference?
* Review passing references to functions
* `const` and references
* Reference variables in range-based for loops
* Potential reference pitfalls
* Raw vs. Smart pointers



## What is a Reference?

* An alias for a variable
* Must be initialized to a variable when declared
* Cannot be null
* Once initialized cannot be made to refer to a different variable
* Very useful as function parameters
* Might be helpful to think of a reference as a constant pointer that is automatically dereferenced



## Usage

* Using references in a range-based `for` loop

  ```cpp
  vector<string> names{"Jack", "Sunny", "Yena"};
  
  for (auto name : names)
      name = "Hello";			// Changes the copy
  
  for (auto name: names)
      cout << name << endl;	// Jack Sunny Yena
  ```

  ```cpp
  vector<string> names{"Jack", "Sunny", "Yena"};
  
  for (auto &name : names)
      name = "Hello";			// Changes the actual
  
  for (auto name: names)
      cout << name << endl;	// Hello Hello Hello
  ```

  ```cpp
  vector<string> names{"Jack", "Sunny", "Yena"};
  
  for (auto const &name : names)
      name = "Hello";			// Compiler error
  ```

  ```cpp
  vector<string> names{"Jack", "Sunny", "Yena"};
  
  for (auto const &name : names)
      cout << name << endl;	// Jack Sunny Yena
  ```

* Passing reference to functions



## L-values & R-values

### L-values

* Values that have names and are addressable

* Modifiable if they are not constants

* Example

  ```cpp
  int x{100};		// x is an l-value
  x = 1000;
  x = 1000 + 20;
  
  string name;	// name is an l-value
  name = "Jack";
  ```

  ```cpp
  100 = x;			// Error: 100 is NOT an l-value
  (1000 + 20) = x;	// Error: (1000 + 20) is NOT an l-value
  
  string name;
  name = "Jack";
  "Jack" = name;		// Error: "Jack" is NOT an l-value
  ```

### R-values

* A value that's not an l-value

  * On the right-hand side of an assignment expression
  * A literal
  * A temporary which is intended to be non-modifiable

* Non-addressable and non-assignable

* Example

  ```cpp
  int x{100};			// 100 is an r-value
  int y = x + 200;	// (x + 200) is an r-value
  
  string name;
  name = "Jack";		// "Jack" is an r-value
  
  int max_num = max(20, 30);	// max(20, 30) is an r-value
  ```

* R-values can be assigned to L-values

  ```cpp
  int x{100};
  int y{0};
  
  y = 100;	// R-value 100 assigned to l-value y
  x = x + y;	// R-value (x + y) assgiend to l-value x
  ```

### L-value References

* The references we've used are l-value references because we are referencing l-values.

  ```cpp
  int x{100};
  
  int &ref1 = x;		// ref1 is reference to l-value
  ref1 = 1000;
  
  int &ref2 = 100;	// Error: 100 is an r-value
  ```

* The same when we pass-by-reference

  ```cpp
  int square(int &n)
  {
      return n * n;
  }
  
  int num{10};
  square(num);	// OK
  square(5);		// Error: can't reference r-value 5
  ```

  

## Pointer Parameters vs. Reference Parameters

* Pass-by-value

  * When the function does NOT modify the actual parameter

  * When the parameter is small and efficient to copy like simple types (`int`, `char`, `double`, etc.)

* Pass-by-reference using a pointer

  * When the function does modify the actual parameter
  * When the parameter is expensive to copy
  * It's OK for the pointer to contain a `nullptr` value

* Pass-by-reference using a pointer to `const`

  * When the function does **NOT** modify the actual parameter
  * When the parameter is expensive to copy
  * It's OK for the pointer to contain a `nullptr` value

* Pass-by-reference using a `const` pointer to `const`

  * When the function does **NOT** modify the actual parameter
  * When the parameter is expensive to copy
  * It's OK for the pointer to contain a `nullptr` value
  * When you don't want to modify the pointer itself

* Pass-by-reference using a reference

  * When the function **DOES** modify the actual parameter
  * When the parameter is expensive to copy
  * The parameter will never be `nullptr`

* Pass-by-reference using a `const` reference

  * When the function does **NOT** modify the actual parameter
  * When the parameter is expensive to copy
  * The parameter will never be `nullptr`
