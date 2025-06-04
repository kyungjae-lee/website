[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Stateful Lambda Expressions

# Stateful Lambda Expressions



## C++ Stateful Lambda Expressions

* The structure of a stateful lambda expression

  ```plain
  [captured_variables] () -> return_type specifiers { };
  ```

  > `captured_variables` - Non-empty capture list: Defines what information/variables should be captured.

* Compilation of stateless lambda expressions 1

  Lambda definition

  ```cpp
  auto l = [] (int x) { std::cout << x; };
  ```

  Compiler-generated closure

  ```cpp
  class CompilerGeneratedName
  {
  public:
      CompilerGeneratedName();
      
      void operator() (int x) { std::cout << x; }
  };
  ```

* Compilation of stateless lambda expressions 2

  Lambda definition

  ```cpp
  int y {10};
  auto l = [y] (int x) { std::cout << x + y; };
  ```

  Compiler-generated closure

  ```cpp
  class CompilerGeneratedName
  {
  private:
      int y;
  public:
      CompilerGeneratedName(int y) : y{y} { };
      
      void operator() (int x) const { std::cout << x + y; }
  };
  ```

* Capture by value

  ```cpp
  int x{100};
  [x] () { std::cout << x; }();		// Displays 100
  ```

* Using `mutable` to modify variables captured by value

  ```cpp
  int x{100};
  
  [x] () mutable
  {
      x += 100;
      std::cout << x;		// Displays 200
  } ();
  
  std::cout << x;			// Displays 100
  ```

* Capture by reference

  ```cpp
  int x{100};
  
  [&x] () { x += 100; }();
  std::cout << x;			// Displays 200
  ```

* Capture by value and reference

  ```cpp
  [x, y]		// Capture both x and y by value
  [x, &y]		// Capture x by value and y by reference
  [&x, y]		// Capture x by reference and y by value
  [&x, &y]	// Capture both x and y by reference
  ```

* Default captures

  ```cpp
  [=]			// Default capture by value
  [&]			// Default capture by reference
  [this]		// Default capture this object by reference
  ```

* Using default and explicit captures

  ```cpp
  [=, &x]		// Default capture by value but capture x by reference
  [&, y]		// Default capture by reference but capture y by value
  [this, z]	// Default capture this by but capture z by value
  ```
