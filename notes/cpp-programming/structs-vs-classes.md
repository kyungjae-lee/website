[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Structs vs. Classes

# Structs vs. Classes



## Introduction

* In addition to define a `class` we can declare a `struct`.
* `struct` comes from the C programming language.
  * In C, we create `struct`s as a container for data, much like a record in many other programming languages.
  * C++ also supports `struct` since it has to be compatible with C.
  * However, it adds the ability to treat `struct`s very much like `class`es.

* Essentially the same as a class except that members are `public` by default. (With `class`es, members are `private` by default.)



## General Guidelines

* `struct`
  * Use `struct` for passive objects with public access
  * Don't declare methods in `struct`
* `class`
  * Use class for active objects with complex behavior an private access
  * Implement getters/setters as needed
  * Implement member methods as needed

* These guidelines are very general. But, remember! Other C++ programmers will likely be modifying and extending your code. So, don't use C++ in a way that's very different from what C++ programmers expect. For example, don't use a struct and then make everything in the struct so it behaves like a class, just use a class instead.



## Examples

* Class

  ```cpp
  class Person
  {
      // Members are private by default
      std::string name;
      std::string get_name();
  };
  
  Person p;
  p.name = "Jack";			// Compiler ERROR - private
  std::cout << p.get_name();	// Compiler ERROR - private
  ```

* Struct

  ```cpp
  struct Person
  {
      // Members are public by default
      std::string name;
      std::string get_name();	
  };
  
  Person p;
  p.name = "Jack";			// OK - public
  std::cout << p.get_name();	// OK - public
  ```
