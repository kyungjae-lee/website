[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > `this` Pointer

# `this` Pointer



## Introduction

* `this` is a reserved keyword that contains the address of the current object. 
  * It's a pointer to the object that's currently being used by the class member methods.

* Can only be used within the class scope
* All member access is done via the `this` pointer.
  * C++ allows you to use member names directly. And, behind the scenes, it's actually using `this` pointer.

* Can be used by the programmer
  * To explicitly access data member and methods
  * To determine if two objects are the same
  * Can be dereferenced (`*this`) to yield the current object
  * When overloading the assignment operator



## Examples

* `Account` class

  ```cpp
  void Account::set_balance(double bal)
  {
      balance = bal;	// 'this->balance' is implied
  }
  ```

  `this` pointer can be used to disambiguate identifier

  ```cpp
  void Account::set_balance(double balance)
  {
      // balance = balance		// Ambiguous (which balance?)
      this->balance = balance;	// Unambiguous
  }
  ```

* To determine object identity

  Sometimes it is useful to know if two objects are the same object

  ```cpp
  int Account::compare_balance(const Account &other)
  {
      if (this == &other)
          std::cout << "The same objects" << std::endl;
      ...
  }
  
  jack_account.compare_balance(jack_account);
  ```
  
  > L3: When the compare logic is involved or computationally expensive, then a quick check to see if the objects are the same could help performance.
