[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Interview Questions</a> > C++

# C++



## What is OOP?

**Object-Oriented Programming (OOP)** is a programming paradigm that organizes code into **objects**, which combine **data** (attributes) and **behavior** (methods). 

OOP enables you to model your programs using real-world objects. This will not only help you think more abstractly, it'll also make your code more robust, easier to debug and easier for others to modify.



## What are 4 Pillars of OOP?

* **Encapsulation**
  * Bundles data and methods that operator on the data within one unit (class).
  * Protects object state by restricting direct access to internal state using access modifiers (e.g., `private`, `protected`, `public`).
* **Abstraction**
  * Hides internal implementation details and exposes only essential parts.
  * Focuses on **what** an object does, not **how** it does it.
* **Inheritance**
  * Creates new classes from existing ones, allowing a class to acquire properties and behaviors of another class (base class).
  * Enables code reuse and a hierarchical class structure.
* **Polymorphism**
  * Allows one interface to be used for different underlying data types.
  * Achieved via function overloading and overriding (especially with virtual functions).



## What is a Class?

A class is a blueprint for creating objects that defines **data** (attributes) and **functions** (methods) that operate on data.

* It does not occupy memory until an object is created.
* A class defines the structure and behavior of its objects.



## What is an Object?

An object is an instance of a class which represents a real-world entity with **state** (data) and **behavior** (functions/methods).

* Created based on a class (blueprint).
* Occupies memory.
* Can access the class's members (variables and functions).



## What is a Virtual Function?

A **virtual function** in C++ is a member function in a base class that you **expect to override in derived classes**. It enables **runtime polymorphism**, allowing the correct function to be called **based on the object type**, not the pointer/reference type.

A virtual function lets you define a function in a base class and override it in derived classes so the correct version is chosen at runtime.



## What is a Virtual Destructor?

A **virtual destructor** ensures that when a base class pointer is used to delete a derived class object, **both the base and derived destructors are called properly**. This is important for proper cleanup and to avoid memory or resource leaks.

You need a virtual destructor **whenever a class is intended to be used polymorphically**, meaning it has virtual functions or might be deleted through a base class pointer.