[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Introduction to Object-Oriented Programming

# Introduction to Object-Oriented Programming



## Overview

OOP enables you to model your programs using real-world objects. This will not only help you think more abstractly, it'll also make your code more robust, easier to debug and easier for others to modify.

* What is Object-Oriented Programming?
* What are classes and objects?
* Declaring classes and creating objects
* Dot and pointer operations
* `public` and `private` access modifiers
* Methods, constructors and destructors
  * `class` methods
  * Default and overloaded constructors
  * Copy and move constructors
  * Shallow vs. deep copy
  * `this` pointer
* `static` class members
* `struct` vs. `class`
* `friend` of a class



## Procedural Programming

* Focus on processes or actions that a program takes
* Programs are typically collection of functions
* Data is declared separately from the functions
* Data is passed as arguments into functions
* Fairly easy to learn
* Breaks up a task into subtasks

### Limitations

* Functions need to know the structure of the data.
  * If the structure of the data changes, many functions that take the data as an argument must be changed accordingly.
  * This could have a ripple effect in the program, and the amount effort needed to change and then test all of the updated functions could be substantial. This gets worse as the size of project gets larger.
  
* As programs get larger they become more:
  * Difficult to understand 
    * Since the number of connections in the program becomes very difficult to trace by hand
  * Difficult to maintain the software
  * Difficult to extend the program
  * Difficult to debug
  * Difficult to reuse code
    * Reusing functions and data structures that we wrote for one program in another program becomes much more difficult since after time we end up with code whose behavior isn't that easy to visualize.
  * Fragile and easier to break
    * When we add new functionality of fix a bug, the chances of introducing another bug is high.
    * This happens much more in large procedural programs than it happens in large object-oriented programs.



## Object-Oriented Programming

Object-Oriented Programming is all about modeling your software in terms of classes and objects which model real-world entities in the problem domain.

* Classes and objects (Abstraction)
  * Focus is on classes that model real-world domain entities
  * Allows developers to think at a higher level of **abstraction** and the details will follow along
  * Used successfully in very large programs
  * Help to deal with complexity
* Encapsulation
  * Objects contain **data** and **operations** that work on that data
  * Encapsulation is an extension of the Abstract Data Type (ADT) in computer science. Now, the data and the operations are together in the class where they belong and not spread across many functions each passing and receiving data.
  * Helps to deal with complexity
* Information-hiding
  * Implementation-specific logic can be hidden in a class so that it's available only within the class.
  * Allows to provide a public interface to the class and hide everything else. Now, we know that the user of the class can't mess with the implementation-specific code since they never knew about it in the first place.
  * More abstraction
  * This makes it easier to test, debug, maintain and extend the code.
* Reusability
  * Since classes are encapsulated units of data and operations that have already been tested, it is easier to reuse them in other applications.
  * This leads to faster development higher quality software.
* Inheritance
  * Allows to create a new class based on an existing class by only adding or modifying the elements that we need for the new class.
  * Reusability
  * Polymorphic classes
* Polymorphism and more...

### Limitations

* Not a panacea
  * OO Programming won't make bad code better
  * Not suitable for all types of problems
    * For example, if you are trying to write a small program that automates tests, using OOP may be overkill.
  * Not everything decomposes to a class
* Learning curve
  * Usually steeper leaning curve, especially for C++
  * Many OO languages, many variations of OO concepts
* Design
  * Usually more up-front design time is required to create good models and hierarchies
* Programs can be:
  * larger in size
  * slower
  * more complex
  
  than non-OOP programs
