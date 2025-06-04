[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Introduction to Lambda Expressions

# Introduction to Lambda Expressions



## Introduction

Lambda expressions were first introduced in C++11 and were later extended in C++14 and C++17. They provide a convenient way to define functionality exactly where it's needed, without writing a lot of extra code.

Types of Lambda Expressions:

- **Stateless Lambda Expression** – does not capture any external variables.
- **Stateful Lambda Expression** – captures variables from the surrounding context.



## Motivation

Prior to C++11, function objects (i.e., functors) and function pointers were commonly used to provide custom behavior, both with the Standard Template Library (STL) and in user-defined code.

* **Function objects** – primarily used with the STL.
* **Function pointers** – more often used as callbacks.

### What's the problem with that?

This approach often leads to the need for writing many short functions that control algorithm behavior. In the STL, these short functions are typically wrapped in small classes to create function objects. However, these classes and functions are frequently located far from where they are actually used, which can make modification, maintenance, and testing more difficult.

Additionally, compilers are generally less effective at optimizing functions that are not defined in-line.

### Case Study

* Using function objects:

  ```cpp
  class Multiplier
  {
  private:
      int num{};
  public:
      Multiplier(int n) : num{n} 	{}
      int operator()(int n) const	{ return num * n; }
  };
  
  std::vector<int> v{1, 2, 3, 4};
  Multiplier mult{10};
  
  std::transform(v.begin(), v.end(), v.begin(), mult);
  // v now contains {10, 20, 30, 40}
  ```

  > L13: Iterates through `v` and calls `mult()` on every element of `v`.

  Or, using an unnamed object, without declaring one, also works:

  ```cpp
  std::vector<int> v{1, 2, 3, 4};
  
  std::transform(v.begin(), v.end(), v.begin(), Multiplier(10));
  // v now contains {10, 20, 30, 40}
  ```

  So, what's wrong with all of this? Well, nothing really. It just becomes tedious to write all these classes, each implementing a specific behavior in an overloaded function call operator. And sometimes, when looking at existing code, the intent isn’t immediately obvious.

* Using generic function objects:

  ```cpp
  template <typename T>
  struct Displayer
  {
      void operator() (const T &data) { std::cout << data << " "; }
  };
  
  Displayer<int> d1;
  Displayer<std::string> d2;
  
  d1(100);		// d1.operator(100);
  				// displays 100
  d2("Jack");		// d2.operator("Jack");
  				// displays Jack
  
  std::vector<int> v1{1, 2, 3, 4, 5};
  std::vector<std::string> v2{"Jack", "Sunny", "Yena"};
  
  std::for_each(v1.begin(), v1.end(), Displayer<int>());
  std::for_each(v1.begin(), v1.end(), d1);
  std::for_each(v2.begin(), v2.end(), d2);
  ```

  > L18: Instantiates a `Displayer` of `int` anonymously.

  We only had to write the class once by defining it as a generic template, which is effective and works well. However, using lambda expressions is even better. They offer greater simplicity and readability.

* Using lambda expressions:

  ```cpp
  std::vector<int> vec1{1, 2, 3, 4, 5};
  std::vector<std::string> vec2{"Jack", "Sunny", "Yena"};
  
  std::for_each(vec1.begin(), vec1.end(), [](int x){ std::cout << x << " "; });
  std::for_each(vec2.begin(), vec2.end(), [](std::string s){ std::cout << s << " ";});
  ```

  > L4, L5: Instead of passing a function object, we can directly use a lambda expression.
  
  With lambda expressions, we no longer need the `Displayer` template class or the function objects `d1` and `d2`. The code becomes more readable, easier to test and debug, and more maintainable. Also, the compiler can more easily optimize this code.
  
  

## Best Practice with Lambda Expressions

Even though lambda expressions can replace function objects and allow us to write more concise and focused code, they cannot completely replace function objects.

A best practice with lambda expressions is to use them when the logic consists of a single statement or just a few simple statements. For more complex logic, it's better to use a function object.

Another benefit of lambda expressions is that the compiler creates a closure object from the lambda. This means we can access the environment in which the lambda was defined, which is a very powerful capability.

The compiler generates unnamed function objects behind the scenes from lambda expressions, and now you understand how they work!



## Structure of a Lambda Expression

The structure of a lambda expression:

```plain
[] () -> return_type specifiers { }; 
```

* `[]` - Capture list: Marks the beginning of a lambda expression and defines the context in which it executes. Based on what is included in the capture list, we can specify to the compiler which variables to capture and whether to capture them by value or by reference.
* `()` - Parameter list: Comma separated list of parameters
* `return_type` - In general, return type can be omitted and the compiler will try to deduce it.
* `specifiers` - Optional specifiers (`mutable` and `constexpr`)
* `{}` - Body of your lambda expression 

### Examples

The following are examples of **stateless** lambda expressions, which have empty capture lists.

* A simple lambda expression (No capture list, no parameter list, no return type):

  ```cpp
  [] () { std::cout << "Hi"; };
  [] () { std::cout << "Hi"; } ();	// Displays Hi
  ```

  > L2: This instantiates a function object from the lambda expression and calls it using the overloaded function call operator. While this isn’t the typical way to use lambda expressions, it clearly demonstrates how they work.

* Passing parameters to lambda expressions:

  ```cpp
  [] (int x) { std::cout << x; };
  [] (int x, int y) { std::cout << x + y; };
  ```

* Assigning a lambda expression to a variable:

  ```cpp
  auto l = [] () { std::cout << "Hi"; };
  l();		// Displays Hi
  ```

  ```cpp
  auto l = [] (int x) { std::cout << x; };
  l(10);		// Displays 10
  l(100);		// Displays 100
  ```

  > L1: Use `auto` keyword to tell the compiler to deduce the type of the lambda expression.
  >
  > L2: Now that we have a variable, we can call the function object created from the lambda expression.

* Returning a value from a lambda expression:

  ```cpp
  auto l = [] (int x, int y) -> int { return x + y; };
  // Or
  auto l = [] (int x, int y) { return x + y; };
  
  std::cout << l(2, 3);		// Displays 5
  std::cout << l(10, 20);		// Displays 30
  ```

  > L3: Since the compiler can very often deduce the type from the return statement itself, it's much more common to see lambda expressions used this way without specifying the return type.
