[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Smart Pointers

# Smart Pointers



## Overview

* Issues with raw pointers
* What are smart pointers?
* Concept of ownership and RAII (Resource Acquisition is Initialization)
* C++ smart pointers:
  * Unique pointers (`unique_ptr`)
    * Used for exclusive ownership of dynamically allocated memory. When ownership is clear, it's easy to determine who is responsible for deleting the pointer—or rather, C++ takes care of it.
  * Shared pointers (`shared_ptr`)
    * Allows multiple owners to share access to the same heap-allocated object. The memory is automatically deallocated when the last `shared_ptr` referencing it is destroyed.
  * Weak pointers (`weak_ptr`)
    * Used in conjunction with `shared_ptr` to avoid cyclic references and dangling pointers. It does not contribute to the reference count.
* Custom deleters - C++ allows specifying a custom function to be called when deleting an object, enabling precise control over how resources are cleaned up.
* Ultimately, we want to write code that contains no explicit `new` or `delete` statements, allowing C++ to handle all memory management. If C++ can automatically clean up heap memory when a pointer is no longer needed, we can avoid memory leaks and eliminate the need to manually track the lifetime of pointers.



## Issues with Raw Pointers

* Raw pointers in C++ provide absolute flexibility with memory management:
  * Allocation
  * Deallocation
  * Lifetime management
* With this flexibility, however, comes complexity. We must explicitly allocate and deallocate storage for heap-dynamic variables and manage their lifetimes. Failing to do so can lead to several common issues:
  * Uninitialized (wild) pointers
    * These pointers may point to arbitrary memory locations. Attempting to store data at such locations can lead to unpredictable behavior or potentially catastrophic results.
  * Memory leaks
    * Occur when heap-allocated storage is not properly deallocated, leading to a gradual loss of available memory.
  * Dangling pointers
    * These are pointers that refer to memory that has already been deallocated. Using such invalid memory can cause undefined behavior and is often difficult to diagnose and fix.
  * Lack of exception safety
    * Even when memory management is done correctly, an exception might be thrown before the cleanup code executes, resulting in leaked memory.
  
  Most defects in programs written in languages that use raw pointers are related to improper pointer management. Smart pointers can help prevent these errors.
* Ownership
  * Who owns the pointer?
  * When should a pointer be deleted?



## What is Smart Pointer? (Ownership & RAII)

* Smart pointers are objects. They are implemented as C++ template classes, which can be instantiated.
* Can only point to heap-allocated memory and automatically call delete when no longer needed.
* Adhere to RAII principles
* C++ smart pointers:
  * Unique pointers (`unique_ptr`)
  * Shared pointers (`shared_ptr`)
  * Weak pointers (`weak_ptr`)
  * Auto pointers (`auto_ptr`) - Deprecated. Will NOT be discussing this one!
* `#include <memory>`
* Defined by class templates
  * Wrapper class that contain and manage a raw pointer. (The actual implementation of the smart pointers is compiler-dependent.)
  * Once created, they can be used in much the same way as raw pointers.
  * Overloaded operators:
    * Dereference (`*`)
    * Member selection (`->`)
    * Pointer arithmetic not supported (`++`, `--`, etc.)
  * Can have custom deleters to explicitly define the exact behavior you want when the pointer is about to be destroyed.

### Example

* A simple example of smart pointers

  ```cpp
  {
      std::smart_pointer<Some_Class> ptr = . . .
          
      ptr->method();
      cout << (*ptr) << endl;
  }
  // ptr will be destroyed automatically when no longer needed
  ```
  
  > Smart pointers help make your code easier to read and write, and maintain with less errors.

### RAII - Resource Acquisition Is Initialization

* RAII is a common idiom or design pattern in software design, based on object lifetime.
* RAII objects are typically allocated on the stack.
* **Resource Acquisition** refers to actions like opening a file, allocating memory, or acquiring a lock.
* **Is Initialization** means the resource is acquired in the constructor during object initialization.
* Resource release happens in the destructor. (e.g., closing a file, deallocating memory, or releasing a lock.)
* Smart pointers are examples of RAII classes because they manage memory resources following this pattern.



## C++ Smart Pointers

### Unique Pointers (`unique_ptr`)

* Simple smart pointer - very efficient!
* `unique_ptr<T>`
  * Points to an object of type `T` on the heap
  * It is unique - there can only be one `unique_ptr<T>` pointing to the object on the heap
  * Owns what it points to
  * Cannot be assigned or copied
  * CAN be moved
  * When the pointer is destroyed, what it points to is automatically destroyed

* Creating, initializing and using

  ```cpp
  {
      std::unique_ptr<int> p1{new int{100}};
      std::cout << *p1 << std::endl;		// 100
      *p1 = 200;
      std::cout << *p1 << std::endl;		// 200
  } // p1 gets deleted automatically
  ```

* Some other useful methods

  ```cpp
  {
      std::unique_ptr<int> p1{new int{100}};
      std::cout << p1.get() << std::endl;		// 0x564388
      p1.reset();		// p1 is now nullptr
      
      if (p1)
          std::cout << *p1 << std::endl;		// Won't execute
  } // p1 gets deleted automatically
  ```

* User defined classes

  ```cpp
  {
      std::unique_ptr<Account> p1{new Account{"Jack"}};
      std::cout << *p1 << std::endl;		// Display account
      
      p1->deposit(1000);
      p1->withdraw(500);
  } // p1 gets deleted automatically
  ```

* Vectors and move

  ```cpp
  {
      std::vector<std::unique_ptr<int>> vec;
      std::unique_ptr<int> ptr{new int{100}};
      vec.push_back(ptr);		// Error - copy not allowed
      vec.push_back(std::move(ptr));
  } // p1 gets deleted automatically
  ```

* `make_unique` (C++14)

  ```cpp
  {
      std::unique_ptr<int> p1 = make_unique<int>(100);
      std::unique_ptr<Account> p2 = make_unique<Account>("Jack", 4000);
      auto p3 = make_unique<Player>("Hero", 100, 100);
  } // p1 gets deleted automatically
  ```

### Shared pointers (`shared_ptr`)

* Provides shared ownership of heap objects

* `shared_ptr<T>`

  * Points to an object of type `T` on the heap
  * It is not unique - there can be many `shared_ptr`s pointing to the same object on the heap.
  * Establishes shared ownership relationship
  * CAN be assigned and copied
  * CAN be moved
  * Doesn't support managing arrays by default
  * When the use count is zero, the managed object on the heap is destroyed

* Creating, initializing and using

  ```cpp
  {
  std::shared_ptr<int> p1{new int{100}};
  std::cout << *p1 << std::endl;		// 100
  *p1 = 200;
  std::cout << *p1 << std::endl;		// 200
  } // p1 gets deleted automatically
  ```

* Some other useful methods

  ```cpp
  // use_count - the number of shared_ptr objects managing the heap object
  std::shared_ptr<int> p1 {new int{100}};
  std::cout << p1.use_count() << std::endl;	// 1
  
  std::shared_ptr<int> p2{p1};				// Shared ownership
  std::cout << p1.use_count() << std::endl;	// 2
  
  p1.reset();			// Decrement the use_count; p1 is nulled out
  std::cout << p1.use_count() << std::endl;	// 0
  std::cout << p2.use_count() << std::endl;	// 1
  } // p1, p2 get deleted automatically
  ```

* User defined classes

  ```cpp
  {
      std::shared_ptr<Account> p1{new Account{"Jack"}};
      std::cout << *p1 << std::endl;	// Display account
      
      p1->deposit(1000);
      p1->withdraw(500);
  } // p1 gets deleted automatically
  ```

* Vectors and move

  ```cpp
  {
      std::vector<std::shared_ptr<int>> vec;
      std::shared_ptr<int> ptr{new int{100}};
      vec.push_back(ptr);		// OK - copy IS allowed
      std::cout << ptr.use_count() << std::endl;		// 2
  } // ptr gets deleted automatically
  ```

* `make_shared` (C++11)

  ```cpp
  {
      std::shared_ptr<int> p1 = std::make_shared<int>(100);	// use_count: 1
      std::shared_ptr<int> p2{p1};		// use_count: 2
      std::shared_ptr<int> p3;
      p3 = p1;	// use_count: 3
  } // p1, p2, p3 get deleted automatically
  ```

  > Use `std::make_shared` - It's more efficient!
  >
  > All 3 pointers point to the SAME object on the heap!
  >
  > When the use_count becomes 0 the heap object is deallocated

### Weak Pointers (`weak_ptr`)

* Provides a non-owning "weak" reference

* `weak_ptr<T>`
  * Points to an object of type `T` on the heap
  * Does not participate in owning relationship
  * Always created from a `shared_ptr`
  * Does NOT increment or decrement reference use count
  * Used to prevent strong reference cycles which could prevent objects from being deleted
  
* Circular or cyclic reference
  * A refers to B
  
  * B refers to A
  
  * Shared strong ownership prevents heap deallocation
  
    
  
    <img src="./img/weak-pointer-1.png" alt="weak-pointer-1" width="600">
  
    
  
  * Solution - make one of the pointers non-owning or 'weak'
  
  * Now heap storage is deallocated properly
  
    
  
  <img src="./img/weak-pointer-2.png" alt="weak-pointer-2" width="600">





## Custom Deleters

* Sometimes when we destroy a smart pointer we need more than to just destroy the object on the heap.
* These are special use-cases
* C++ smart pointers allow you to provide custom deleters
* Lots of way to achieve this
  * Functions
  * Lambdas
  * Others ...

### Example

* Functions

  ```cpp
  void my_deleter(Some_Class *raw_pointer)
  {
      // Your custom deleter code
      delete raw_pointer;
  }
  
  share_ptr<Some_Class> ptr{new Some_class{}, my_deleter};
  ```

  ```cpp
  void my_deleter(Test *ptr)
  {
      cout << "In my custom deleter" << endl;
      delete ptr;
  }
  
  share_ptr<Test> ptr{new Test{}, my_deleter};
  ```

* Lambdas

  ```cpp
  shared_ptr<Test> ptr(new Test{100}, [](Test *ptr) {
      cout << "\tUsing my custom deleter" << endl;
      delete ptr;
  });
  ```
