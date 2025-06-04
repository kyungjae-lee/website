[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Stateful Lambda Expressions

# Stateful Lambda Expressions



## C++ Stateful Lambda Expressions

A **stateful lambda expression** in C++ is a lambda that **captures variables from its surrounding scope**, allowing it to retain state between calls or access external context. This is done through the **capture list**, the part in square brackets `[]` before the parameter list.

### The Structure of a Stateful Lambda Expression

```plain
[captured_variables] () -> return_type specifiers { };
```

> `captured_variables` - Non-empty capture list: Defines what information/variables should be captured.

### Compilation of Stateless Lambda Expressions

* Lambda definition:

  ```cpp
  auto l = [] (int x) { std::cout << x; };
  ```

* Compiler-generated closure:

  ```cpp
  class CompilerGeneratedName
  {
  public:
      CompilerGeneratedName();	// default constructor
      
      void operator() (int x) { std::cout << x; }	// overloaded operator() function
  };
  ```

  > A compiler-generated closure is an **unnamed class automatically created by the compiler** to represent a lambda expression in C++. It contains:
  >
  > 1. An overloaded `operator()` – defines the behavior (the lambda body).
  > 2. Captured variables as data members – if the lambda is *stateful*.
  > 3. Possibly a default constructor and copy/move constructors.

  The class behind a lambda expression isn't generated until compile time. This explains why the `auto` keyword must be used when declaring a variable to hold a lambda. At the point we define the lambda, its type doesn't yet exist—it will only be created by the compiler during compilation. Since the type is unique and unnamed, only the compiler can know and assign it.

  When we assign a lambda expression to a variable, we're actually instantiating an object of the compiler-generated closure class. This involves an implicit call to the constructor of that class. In essence, lambda expressions work behind the scenes by creating unnamed function objects at compile time, encapsulating both behavior and any captured state.

### Compilation of Stateful Lambda Expressions

* Lambda definition:

  ```cpp
  int y {10};
  auto l = [y] (int x) { std::cout << x + y; };
  ```

* Compiler-generated closure:

  When a stateful lambda expression is instantiated, the compiler creates a unique function object **using a parameterized constructor**. This object stores the captured variables as member variables, allowing the lambda to retain context from its surrounding scope.

  ```cpp
  class CompilerGeneratedName
  {
  private:
      int y;
  public:
      CompilerGeneratedName(int y) : y{y} { };	// parametrized constructor
      
      void operator() (int x) const { std::cout << x + y; }
  };
  ```

  This time, the overloaded `operator()` function is a **constant member function**, meaning it cannot modify any member variables of the instantiated object. As a result, the member variable `y` cannot be changed. This is because, by default, all variables captured **by value** in a lambda are captured as **const**. While this helps ensure safety, it can be limiting when we need to modify captured values. To address this, C++ provides several ways to capture variables with more flexibility.

### The Ways a Lambda Expression can Capture "Variables"

* Capture by value (default capturing mode):

  ```cpp
  int x{100};
  [x] () { std::cout << x; }();		// Displays 100
  ```

  > Remember! A variable being captured by value is actually being captured by `const` value which won't be modifiable within the lambda.

* Using `mutable` to modify variables captured by value:

  ```cpp
  int x{100};
  
  [x] () mutable
  {
      x += 100;
      std::cout << x;		// Displays 200
  } ();
  
  std::cout << x;			// Displays 100
  ```

  > L3: The keyword `mutable` is used to tell the compiler to generate the lambda's operator function as a non-const member function. This way the variable captured by value can be modified within the lambda. Remember! The captured variable is still a copy of the original variable passed to the lambda.

* Capture by reference:

  ```cpp
  int x{100};
  
  [&x] () { x += 100; }();
  std::cout << x;			// Displays 200
  ```

* Capture by value and reference:

  ```cpp
  [x, y]		// Capture both x and y by value
  [x, &y]		// Capture x by value and y by reference
  [&x, y]		// Capture x by reference and y by value
  [&x, &y]	// Capture both x and y by reference
  ```

* Default captures:

  A default capture allows a lambda to capture "all" variables referenced within its body according to the defined capture mode.

  ```cpp
  [=]			// Default capture by value
  [&]			// Default capture by reference
  [this]		// Default capture this object by reference
  ```

  > L3: The keyword `this` indicates that all member variables of the current object, as referenced within the lambda, should be captured by reference.

* Using default and explicit captures:

  This approach allows you to mix default capture modes (`[=]` or `[&]`) with explicitly captured variables, giving you fine-grained control over how each variable is captured. 

  ```cpp
  [=, &x]		// Default capture by value but capture x by reference
  [&, y]		// Default capture by reference but capture y by value
  [this, z]	// Default capture this by but capture z by value
  ```

  > The default capture must come first in the capture list. Also, the explicit capture cannot be the same as the default. Otherwise, the lambda won't compile.



## Note

With so many possible combinations of default and explicit captures, it's difficult to cover them all. However, what we've discussed should equip you for most situations where stateful lambdas are needed.
