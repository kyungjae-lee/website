[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Classes & Objects

# Classes & Objects



## Classes & Objects

### Classes

* Model real-world entities
* Blueprint from which objects are created
* A user-defined data-type
* Has attributes (data; instance variables)
* Has methods (functions)
* Can hide data and methods that are only used internally by the class (By using access modifiers)
* The goal of a class is to provide a well-defined public interface that the user of the class can easily use to solve their problem.

* Examples:
  * `Account`
  * `Employee`
  * `Image`
  * `std::vector`
  * `std::string`

### Objects

* Created from a class
* Represents a specific instance of a class
* Can create many objects out of a class
* Each has its own identity
* Each can use the defined class methods

* Examples:

  * Jack's account is an instance of an `Account`.
  * Sunny's account is an instance of an `Account`.

  Each has its own balance, can make deposits, withdrawals, etc.

### Examples

* `Player` class and objects

  ```cpp
  // Class declaration
  
  class Player
  {
      // Attributes (or instance variables)
      std::string name;
      int health;
      int xp;
      
      // Methods
      void talk(std::string text_to_say);
      bool is_dead();
  };
  ```

  ```cpp
  // Object instantiataion
  
  Player jack;
  Player hero;
  
  Player *enemy = new Player();	// Allocated dynamically on the heap
  delete enemy;
  ```

* `Account` class and objects

  ```cpp
  // Class declaration
  
  class Account
  {
      // Attributes (or instance variables) 
      std::string name;
      double balance;
      
      // Method
      bool withdraw(double amount);
      bool deposit(double amount);
  };
  ```

  ```cpp
  Account jack_account;
  Account sunny_account;
  
  Account *yena_account = new Account();	// Allocated dynamically on the heap
  delete yena_account;
  
  Account accounts[] {jack_account, sunny_account};
  
  std::vector<Account> accounts1 {jack_account};
  accounts1.push_back(sunny_account);
  ```

  > Once declared, a class can be used just like any other C++ primitive data types.



## Accessing Class Members

* You can access  class **attributes** and class **methods**.

* Some class members will not be accessible (i.e., hidden members)

* Need an object to access instance variables

* If you have an **object**, you can access the class members by using the **dot operator (`.`**).

  ```cpp
  Account jack_account;
  
  jack_account.balance;
  jack_account.deposit(1000.00);
  ```

* If you have a **pointer to an object**, you can access the class members by using the **arrow operator or member of pointer operator (`->`)**.

  ```cpp
  Account jack_account = new Account();
  
  // 1. Dereference the poiner, then use the dot operator
  (*jack_account).balance;
  (*jack_account).deposit(1000.00);
  
  // 2. Use the arrow operator (member of pointer operator)
  jack_account->balance;
  jack_account->deposit(1000.00);
  ```

  

## Class Member Access Modifiers (`public`, `private`, `protected`)

* `public` - Accessible everywhere
* `private` - Accessible only by other members of the same class or by friends of the class
* `protected` - Used with inheritance

### Examples

* `Player` class

  ```cpp
  class Player
  {
  private:
      std::string name;
      int health;
      int xp;
  public:
      void talk(std::string text_to_say);
      bool is_dead();
  };
  ```

  ```cpp
  Player jack;
  jack.name = "Jack";		// Compiler error
  jack.health = 1000;		// Compiler error
  jack.talk("Hello!");	// OK
  
  Player *enemy = new Player();
  enemy->xp = 100;		// Compiler error
  enemy->talk("Got you!");// OK
  
  delete enemy;
  ```

  > If you try to directly access a private class member from outside of the class, you will get a compiler error.

* `Account` class

  ```cpp
  class Account
  {
  private:
      std::string name;
      double balance;
  public:
      bool withdraw(double amount);
      bool deposit(double amount);
  };
  ```

  ```cpp
  Account jack_account;
  jack_ccount.balance = 1000.00;			// Compiler error
  jack_account.deposit(1000.00);			// OK (Accessing a private member through another member of the class)
  jack_account.name = "Jack's Account";	// Compiler error
  
  Account *sunny_account = new Account();
  
  sunny_account->balance = 10000.0;		// Compiler error
  sunny_account->withdraw(10000.0);		// OK
  
  delete sunny_account;
  ```

  > This is very powerful, because, for example, if we have an object whose balance is 20 million dollars and we know that that must have been an error, then the only place where that error could have happened in this example is in the deposit method. There's no way that any other part of the program could have changed that value since the value is private. 
  >
  > This makes testing and debugging code much easier.



## Implementing Member Methods

* Very similar to how we implemented functions
* Member methods have access to member attributes (Don't need to pass them as arguments as we do with regular functions!)
* Can be implemented inside the class declaration
  * If you do this, the method becomes implicitly inline.
  * While this is okay for small and relatively simple methods, it is a good practice to implement larger, more complex methods outside of the class declaration.

* Can be implemented outside the class declaration
  * To do this you need to use `Class_name::method_name` to tell the compiler that you are implementing a method for a specific class.
  * The compiler can then type check the method implementation when it sees them.

* Can separate specification from implementation. This makes the class much easier to manage.
  * `.h`/`.hpp` file for the class declaration
  * `cpp` file for the class implementation

### Examples

* Implementing member methods INSIDE the class declaration

  ```cpp
  class Account
  {
  private:
      double balance;
  public:
      void set_balance(double bal) { balance = bal; }
      double get_balance() { return balance; }
  };
  ```

* Implementing member methods OUTSIDE the class declaration

  ```cpp
  class Account
  {
  private:
      double balance;
  public:
      void set_balance(double_bal);
      double get_balance();
  };
  
  void Account::set_balance(double bal) { balance = bal; }
  double Account::get_balance() { return balance; }
  ```

  > Need to tell the compiler that you are implementing a method for a specific class. The compiler can then type check the method implementation when it sees them.

* Separating specification from implementation

  ```cpp
  /* Account.h (Specification) */
  
  /* Include guard */
  #ifndef ACCOUNT_H
  #define ACCOUNT_H
  
  // Account class declaration
  class Account
  {
  private:
      double balance;
  public:
      void set_balance(double bal);
      double get_balance();
  };
  
  #endif
  ```
  
  > The include guard can be replaced by `#pragma once`. But, do check first if your compiler support this directive, because some don't!
  
  ```cpp
  /* Account.cpp (Implementation) */
  
  #include "Account.h"
  
  void Account::set_balance(double bal) { balance = bal; }
  double Account::get_balance() { return balance; }
  ```
  
  > Notice that we included `accoun.h` and it's in double quotes. Includes with double quotes tell the compiler to include header files that are local to this project. The compiler knows where those are.
  >
  > Includes with angled brackets with no extensions (e.g., `#include <iostream>`) are used to include system header files and the compiler knows where these are located.
  
  Main file
  
  ```cpp
  /* main.cpp */
  
  #include <iostream>
  #include "Account.h"
  
  int main()
  {
      Account jack_account;
      jack_account.set_balance(1000.00);
      double bal = jack_account.get_balance();
      
      std::cout << bal << std::endl;	// 1000
      return 0;
  }
  ```
  
  > Include `.h` files and never include `.cpp` files!
  >
  > When the program is compiled, both the `main.cpp` and the `account.cpp` files are compiled and then linked to produce the executable.
