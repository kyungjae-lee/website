[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Generic Programming & Templates

# Generic Programming & Templates



## Overview

* Generic programming / Meta-programming
  * Preprocessor macros
  * Function templates
  * Class templates

  

## Generic Programming with Macros

* Generic programming

  "Writing code that works with a variety of types as arguments, as long as those argument types meet specific syntactic and semantic requirements." - Bjarne Stroustrup

* Macros - **BEWARE**!

* Function templates

* Class templates

### Macros (`#define`)

* C++ preprocessor directives

* No type information

* Simple substitution

* Example of macros

  Source file (.cpp)

  ```cpp
  #define MAX_SIZE	100
  #define PI 			3.14159
  
  if (num > MAX_SIZE)
      std::cout << "Too big";
  
  double area = PI * r * r;
  ```

  Expanded source file (.i)

  ```cpp
  #define MAX_SIZE	100			// Removed
  #define PI 			3.14159		// Removed
  
  if (num > 100)
      std::cout << "Too big";
  
  double area = 3.14159 * r * r;
  ```

  > All macros will be expanded by the preprocessor. (Textual substitution)

* Making code more generic by using macro with arguments instead of multiple functions

  ```cpp
  #define MAX(a, b) ((a > b) ? a : b)
  
  std::cout << MAX(10, 20)	<< std::endl;	// 20
  std::cout << MAX(2.4, 3.5)	<< std::endl;	// 3.5
  std::cout << MAX('A', 'C')	<< std::endl;	// C
  ```

  Instead of 

  ```cpp
  int max(int a, int b) { return (a > b) ? a : b; }
  double max(double a, double b) { return (a > b) ? a : b; }
  char max(char a, char b) { return (a > b) ? a : b; }
  ```

* Caution! Use parenthesis generously!

  ```cpp
  #define SQUARE(a) 	a * a
  
  result = SQUARE(5);		// Expect 25
  result = 5 * 5;			// Get 25
  
  result = 100 / SQUARE(5);	// Expect 4
  result = 100 / 5 * 5;		// Get 100
  ```

  Instead, guard each argument with parenthesis

  ```cpp
  #define SQUARE(a) 	((a) * (a))
  
  result = SQUARE(5);			// Expect 25
  result = ((5) * (5));		// Still get 25
  
  result = 100 / SQUARE(5);	// Expect 4
  result = 100 / ((5) * (5));	// Now we get 4
  ```

  

## Generic Programming with Function Templates

* What is a C++ Template?
  * Blueprint
  * Function and class templates
  * Allow **plugging-in** any data type
  * Compiler generates the appropriate function/class from the blue print
  * Generic programming / meta-programming

### Example

* Revisiting the previous example, we can replace the type we want to generalize with a name, say `T`. But, now this won't compile.

  ```cpp
  T max(T a, T b) { return (a > b) ? a : b; }
  ```

  We need to tell the compiler that this is a **template function**, and that  `T` is the **template parameter**.

  ```cpp
  template <typename T>
  T max(T a, T b) { return (a > b) ? a : b; }
  ```

  Can use `class` instead of `typename`

  ```cpp
  template <class T>
  T max(T a, T b) { return (a > b) ? a : b; }
  ```

  Now the compiler can generate the appropriate function from the template.

  Note, this happens at compile-time.

  ```cpp
  int a{10};
  int b{20};
  
  std::cout << max<int>(a, b);
  ```

* Many times the compiler can deduce the type and the template parameter is not needed. Depending on the type of `a` and `b`, the compiler will figure it out.

  ```cpp
  std::cout << max<double>(c, d);
  std::cout << max(c, d);
  ```

* And we can use **almost** any type we need.

  ```cpp
  char a{'A'};
  char b{'B'};
  
  std::cout << max(a, b) << std::endl;
  ```

* Notice that the type MUST support the `>` operator either natively or as an overloaded operator (`operator>`).

  ```cpp
  template <typename T>
  T max(T a, T b) { return (a > b) ? a : b; }
  ```

  The following will not compile unless `Player` overloads `operator>`.

  ```cpp
  Player p1{"Hero", 100, 20};
  Player p2{"Enemy", 99, 3};
  
  std::cout << max<Player>(p1, p2);
  ```

* Templates can have multiple parameters, and their types can be different.

  ```cpp
  template <typename T1, typename T2>
  void func(T1 a, T2 b) { std::cout << a << " " << b; }
  ```

  When we use the function we provide the template parameters. often the compiler can deduce them.

  ```cpp
  func<int, double>(10, 5.4);
  func(10, 5.4);
  ```

