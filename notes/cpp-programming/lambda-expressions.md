[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Lambda Expressions

# Lambda Expressions



## Overview

* What is lambda expression?
  * Motivation
  * Review of function objects (functors)
  * Relation between lambdas and function objects

* Structure of a lambda expression
* Types of lambda expressions
  * Stateless lambda expression
  * Stateful lambda expression (capturing context)

* Lambdas and the STL



## Motivation

* Prior to C++11
  * Function objects
  
  * Function pointers
  
  * We often write many short functions that control algorithms
  
  * These short functions would be encapsulated in small classes to produce function objects

  * Many times the classes and functions are far removed from where they are used leading to modification, maintenance, and testing issues

  * Compiler cannot effectively inline these functions for efficiency.
  

### Example

* Function objects

  ```cpp
  class Multiplier
  {
  private:
      int num{};
  public:
      Multiplier(int n) : num{n} 		{}
      int operator()(int n) const 	{ return num * n; }
  };
  
  std::vector<int> vec{1, 2, 3, 4};
  Multiplier mult{10};
  
  std::transform(vec.begin(), vec.end(), vec.begin(), mult);
  // vec now contains {10, 20, 30, 40}
  ```

* Generic function objects

  ```cpp
  template <typename T>
  struct Displayer
  {
      void operator() (const T &data) { std::cout << data << " "; }
  };
  
  Displayer<int> d1;
  Displayer<std::string> d2;
  
  d1(100);		// d.operator(100);
  				// displays 100
  d2("Jack");		// d.operator("Jack");
  				// displays Jack
  
  std::vector<int> vec1{1, 2, 3, 4, 5};
  std::vector<std::string> vec2{"Jack", "Sunny", "Yena"};
  
  std::for_each(vec1.begin(), vec1.end(), Displayer<int>());
  std::for_each(vec1.begin(), vec1.end(), d1);
  std::for_each(vec2.begin(), vec2.end(), d2);
  ```

* Using a lambda expression

  ```cpp
  std::vector<int> vec1{1, 2, 3, 4, 5};
  std::vector<std::string> vec2{"Jack", "Sunny", "Yena"};
  
  std::for_each(vec1.begin(), vec1.end(), [](int x){ std::cout << x << " "; });
  std::for_each(vec2.begin(), vec2.end(), [](std::string s){ std::cout << s << " ";});
  ```

  

## Structure of a Lambda Expression

* The structure of a lambda expression

  ```plain
  [] () -> return_type specifiers { }; 
  ```

  >* `[]` - Capture list: Defines the start of the lambda (Defines the context in which the lambda executes)
  >* `()` - Parameter list: Comma separated list of parameters
  >* `return_type` - Return type can be omitted and the compiler will try to deduce it.
  >* `specifiers` - Optional specifiers
  >* `{}` - Body of your code

### Example

* A simple lambda expression

  ```cpp
  [] () { std::cout << "Hi"; };
  ```

  ```cpp
  [] () { std::cout << "Hi"; } ();	// Displays Hi
  ```

* Passing parameters to lambda expressions

  ```cpp
  [] (int x) { std::cout << x; };
  ```

  ```cpp
  [] (int x, int y) { std::cout << x + y; };
  ```

* Assigning a lambda expression to a variable

  ```cpp
  auto l = [] () { std::cout << "Hi"; };
  l();		// Displays Hi
  ```

  ```cpp
  auto l = [] (int x) { std::cout << x; };
  l(10);		// Displays 10
  l(100);		// Displays 100
  ```

* Returning a value from a lambda expression

  ```cpp
  auto l = [] (int x, int y) -> int { return x + y; };
  // Or
  auto l = [] (int x, int y) { return x + y; };
  
  std::cout << l(2, 3);		// Displays 5
  std::cout << l(10, 20);		// Displays 30
  ```

  

## C++ Stateless Lambda Expressions

* Simple stateless lambda expressions

  ```cpp
  [] () { std::cout << "Hi"; } ();		// Displays Hi
  int x{10};
  [] (int x) { std::cout << x; }(100);
  ```

  ```cpp
  const int n{3};
  int num[n]{10, 20, 30};
  
  auto sum = [] (int nums[], int n)
  {
      int sum{0};
      for (int i = 0; i < n; i++)
          sum += nums[i];
      return sum;
  };
  
  std:: cout << sum(nums, 3);		// Displays 60
  ```

* Using values and references as lambda parameters

  ```cpp
  [] (int x) { std::cout << x; };
  [] (int &x) { std::cout << x; };
  ```

  > * `int x` - Value parameter
  > * `int &x)` - Reference parameter

  ```cpp
  int test_score1{88};
  int test_score2{75};
  
  auto bonus = [] (int &score1, int &score2, int bonus_points)
  {
      score1 += bonus_points;
      score2 += bonus_points;
  };
  
  bonus(test_score1, test_score2, 5);
  
  std::cout << "test_score1: " << test_score1 << std::endl;	// Displays 93
  std::cout << "text_score2: " << test_score2 << std::endl;	// Displays 80
  ```

* Using pointers as lambda parameters

  ```cpp
  int x;
  auto l = [] (int *x) { std::cout << *x; };
  l(&x);	// '&': Referencing (address-of) operator
  ```

  ```cpp
  int test_score1{88};
  int test_score2{75};
  
  auto bonus = [] (int *score1, int *score2, int bonus_points)
  {
      *score1 += bonus_points;
      *score2 += bonus_points;
  };
  
  bonus(&test_score1, &test_score2, 5);
  
  std::cout << "test_score1: " << test_score1 << std::endl;	// Displays 93
  std::cout << "text_score2: " << test_score2 << std::endl;	// Displays 80
  ```

* Using arrays and vectors as lambda reference parameters

  ```cpp
  std::vector<int> test_scores{ 93, 88, 75, 68, 65 };
  
  auto bonus = [] (std::vector<int> &scores, int bonus_points)
  {
      for (int &score : scores)
          score += bonus_points;
  };
  
  bonus(test_scores, 5);
  
  std::cout << "test_scores: " << std::endl;
  std::cout << text_scores[0] << std::endl;	// Displays 98
  std::cout << text_scores[1] << std::endl;	// Displays 93
  std::cout << text_scores[2] << std::endl;	// Displays 80
  std::cout << text_scores[3] << std::endl;	// Displays 73
  std::cout << text_scores[4] << std::endl;	// Displays 70
  ```

* Using `auto` as lambda parameter type specifiers

  ```cpp
  int num1{10};
  float num2 {20.5};
  
  auto l = [] (auto x) { std::cout << x; };
  
  l(num1);
  l(num2);
  ```

  ```cpp
  std::vector<int> test_scores1 {93, 88, 75, 68, 65 };
  std::vector<float> test_scores2 {88.5, 85.5, 75.5, 68.5, 65.5};
  
  auto bonus = [] (auto &scores, int bonus_points)
  {
      for (auto &score : scores)
          score += bonus_points;
  };
  
  bonus(test_scores1, 5);		// Valid
  bonus(test_scores2, 5);		// Valid
  ```

* Using lambda expressions as function parameters

  ```cpp
  #include <functional>		// For std::function
  
  void foo(std::function<void(int)> l) { l(10); }		// C++14
  //					   ---- ---
  //				return-type parameter-type
  
  // or
  
  void foo(void (*)(int))	{ l(10); }					// C++14
  
  // or
  
  void foo(auto l) { l(10); }							// C++20
  ```

* Returning lambda expressions from functions

  ```cpp
  #include <functional>		// For std::function
  
  std::function<void(int)> foo() { return [] (int x) { std::cout << x; }; }
  
  // or
  
  void (*foo())(int) { return [] (int x) { std::cout << x; }; }
  
  // or
  
  auto foo() { return [] (int x) { std::cout << x; }; }
  ```

  > All 3 versions are used the same way.
  >
  > ```cpp
  > auto l = foo();
  > l(10);		// Displays 10
  > ```

* Using lambda expressions as function parameters

  ```cpp
  foo([] (int x) { std::cout << x; });
  
  // or
  
  auto l = [] (int x) { std::cout << x; };
  foo(l);
  ```

* Using lambda expressions as predicates

  ```cpp
  void print_if(std::vector<int> nums, bool (*predicate)(int))
  {
      for (int i : nums)
      {
          if (predicate(i))
              std::cout << i;
      }
  }
  
  int main()
  {
      std::vector<int> nums{1, 2, 3};
      
      print_if(nums, [] (auto x) { return x % 2 == 0; });	// Displays evens
      print_if(nums, [] (auto x) { return x % 2 != 0; });	// Displays odds
      
      return 0;
  }
  ```



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
