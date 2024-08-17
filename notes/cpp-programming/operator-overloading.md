[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Operator Overloading

# Operator Overloading



## Overview

C++ allows the programmer to overload most operators to work with user-defined classes. This is intended to make code more readable and writable by allowing the use of familiar operators in a familiar manner, but with our own classes and objects.

* What is operator overloading?
* Overloading the assignment operator (`=`)
  * Copy semantics
  * Move semantics
* Overloading operators as member functions
* Overloading operators as global functions
* Overloading stream insertion (`<<`) and extraction operators (`>>`)




## What is Operator Overloading?

* Using traditional operators such as `+`, `=`, `*`, etc. with user-defined types.
* Allows user-defined types to behave and feel similar to built-in types
* Can make code more readable and writable
* Not done automatically (except for the assignment operator)
  
    They must be explicitly defined.
* The only operator that the compiler provides a default implementation for is the assignment operator (`=`). This is because the compiler must be able to assign one object to another. All the other operators that can be overloaded must be explicitly defined by the programmer.
* Suppose we have a `Number` class that models any number.
  
    **Using functions:**
    
    ```plain
    Number result = multiply(add(a, b), divide(c, d));
    ```
    
    **Using member functions:**

  ```plain
  Number result = (a.add(b)).multiply(c.divide(d));
  ```

  These statements are very unreadable and very complicated to write. Why can't we use the basic arithmetic symbols we have been using since elementary school now? $$\to$$ We can! And that's the power of **operator overloading**.
  
  **Using overloaded operators:**
  
  ```plain
  Number result = (a + b) * (c / d);
  ```
  Now it looks and feels and behaves like the built-in C++ types.
* Operator overloading is syntactic sugar. Behind the scenes, we're still calling functions.




## What Operators can be Overloaded?

* The majority of C++'s operators can be overloaded.
* The following operators CANNOT be overloaded:
  
    `::` (scope resolution operator)
    
    `:?` (conditional operator)
    
    `.*` (pointer to member operator)
    
    `.` (dot operator)
    
    `sizeof`
* Remember, just because an operator can be overloaded doesn't mean you should. Don't overload it unless it makes sense and makes your code more usable, more readable and more writable.
  
* We can make the operator mean anything we want. We want to make sure that when we do overload operators it makes sense and the users of the class know about it.
  




## Basic Rules of Operator Overloading

* Precedence and Associativity CANNOT be changed.
* "-arity" CANNOT be changed. (i.e., can't make the division operator "unary")
* Can't overload operators for primitive type (e.g., `int`, `double`, etc.)
* Can't create new operators
* `[]`, `()`, `->`, and the assignment operator (`=`) MUST be declared as member functions.
* Other operators can be declared as member functions or global functions.




## Examples of Operator Overloading

**Operator Overloading used on C++ Built-In Types:**

* `int`
  ```plain
  a = b + c
  a < b
  std::cout << a
  ```
* `double`
  ```plain
  a = b + c
  a < b
  std::cout << a
  ```
* `long`
  ```plain
  a = b + c
  a < b
  std::cout << a
  ```

**Operator Overloading used on User-Defined Types:**

* `std::string`
  ```plain
  s1 = s2 + s3
  s1 < s2
  std::cout << s1
  ```
* `Player`
  ```plain
  p1 < p2
  p1 == p2
  std::cout << p1
  ```
* `Mystring`
  ```plain
  s1 = s2 + s3
  s1 < s2
  s1 == s2
  std::cout << s1
  ```
  `Mystring` class declaration: (Incomplete)
  
  See *Complete `Mystring` Class* section for the complete class implementation. 
  
  ```cpp
  #ifndef MYSTRING_H
  #define MYSTRING_H
  
  class Mystring
  {
  private:
      char *str;    // pointer to a char[] that holds a C-style string
  
  public:
      Mystring();                           // default constructor
      Mystring(const char *s);              // parameterized constructor
      Mystring(const Mystring &source);     // copy constructor
      ~Mystring();                          // destructor
      void display() const;
      int get_length() const;               // getter
      const char* get_str() const;          // getter
  };
  
  #endif // MYSTRING_H
  ```
  `Mystring` class implementation: (Incomplete)
  
  See *Complete `Mystring` Class* section for the complete class implementation. 
  
  ```cpp
  #include <cstring>    // behind the scenes, Mystring class uses c-string library
  #include <iostream>
  #include "Mystring.h"
  
  // default constructor
  Mystring::Mystring()
      :str(nullptr)
  {
      str = new char[1];
      *str = '\0';
  }
  
  // parameterized constructor
  Mystring::Mystring(const char *s)
      :str(nullptr)
  {
      // if an empty string has been passed, create an empty string ""
      if (s == nullptr)
      {
          str = new char[1];
          *str = '\0';
      }
      else
      {
          str = new char[std::strlen(s) + 1];
          std::strcpy(str, s);
      }
  }
  
  // copy constructor
  Mystring::Mystring(const Mystring &source)
      : str(nullptr)
  {
      str = new char[std::strlen(source.str) + 1];
      std::strcpy(str, source.str);
  }
  
  // destructor
  Mystring::~Mystring()
  {
      delete[] str;
  }
  
  // display method
  void Mystring::display() const 
  {
      std::cout << str << ":" << get_length() << std::endl;
  }
  
  // length getter
  int Mystring::get_length() const 
  { 
      return std::strlen(str);
  }
  
  // string getter
  const char* Mystring::get_str() const
  {
      return str;
  }
  ```
  Test driver: 
  
  See *Complete `Mystring` Class* section for the complete class implementation. 
  
  ```cpp
  #include <iostream>
  #include "Mystring.h"
  
  int main(int argc, char *argv[])
  {
      Mystring empty;           // default constructor
      Mystring larry("Larry");  // overloaded constructor
      Mystring stooge(larry);   // copy constructor
  
      empty.display();
      larry.display();
      stooge.display();
  
      return 0;
  }
  ```
  ```text
  :0
  Larry:5
  Larry:5
  ```




## Copy Assignment Operator Overloading

* When you don't provide a user-defined copy assignment operator, C++ will generate a default assignment operator used for assigning one object to another.
  ```plain
  Mystring s1("Frank");
  Mystring s2 = s1;    // NOT assignment because s2 hasn't been created yet!
                       // same as Mystring s2(s1);
  
  s2 = s1;             // assignment since s2 has already been created and initialized
  ```
  Don't confuse "initialization" with "assignment". Initialization is done by constructors when we create new objects.
  
* Default behavior is memberwise assignment (**shallow copy**).

* If we have raw pointer data member, we must **deep copy**.

* Copy assignment operator works with **L-value references**.

* **Overloading the copy assignment operator (deep copy):**

  ```plain
  Type &Type::operator=(const Type &rhs);
  ```
  Example:
  ```plain
  Mystring& Mystring::operator=(const Mystring &rhs);   // method name is 'operator='
  
  s2 = s1;              // we write this
  s2.operator=(s1);     // compiler will convert 's2 = s1' into this
                        // 'operator=' method is called
  ```
  > Returning the reference is important because we don't want to make an extra copy of what we are returning and we want to allow chain assignments such as `s1 = s2 = s3`.

* The object on the left-hand side of an assignment statement is referred to by the `this` pointer. The object on the right-hand side is being passed into the method. The semantics is that the object on the LHS is going to be overwritten by the object on the RHS. 
  
  1. First, we need to deallocate anything it refers to on the heap. (If we don't do this, we'll orphan this memory and end up with a memory leak.)
  
  2. Then we need to allocate the space in the LHS object for the RHS side object's data, and then we finally copy the data over to the left side from the right side.
  
* **Overloaded copy assignment operator for `Mystring` class:**
  
  ```cpp
  Mystring& Mystring::operator=(const Mystring &rhs)
  {
      // check for self assignment
      if (this == &rhs)
          return *this;     // if so, return current object
  
      delete[] str;         // deallocate storage for 'this->str' since we are overwriting it
                            // delete[] this->str; also works
      str = new char[std::strlen(rhs.str) + 1];  // allocate storage for the deep copy
      std::strcpy(str, rhs.str);    // perform the copy
  
      return *this;     // return the LHS object by reference to allow chain assignment
  }
  ```
  
* **Test driver:**
  See *Complete `Mystring` Class* secction for the complete class implementation. 
  
  ```cpp
  #include <iostream>
  #include "Mystring.h"
  
  int main(int argc, char *argv[])
  {
      Mystring a("Hello");      // parameterized constructor
      Mystring b;               // default constructor 
      b = a;                    // copy assignment operator
                                // b.operator=(a)
      b = "This is a test";     // b.operator=("This is a test");
  
      return 0;
  }
  ```
  > In line $$10$$, the copy assignment operator will be called, a temporary object will be created and it will be assigned over. Once the assignment is done, the destructor will be called to destroy the temporary object.
  
  


## Move Assignment Operator (=) Overloading

* You can choose to overload the move assignment operator.
    - C++ will use the copy assignment operator (copy assignment operator) by default, if
      necessary.
      ```cpp
      Mystring s1;              // empty string
      s1 = Mystring("Frank");   // move assignment will be called since we are providing R-value reference
           -----------------                                                             -----------------
           temporary object is created                                                        "Frank"
      ```
    
* If we have raw pointer we should overload the move assignment operator for efficiency.

* Move assignment operator works with **R-value references**. (Think temporary unnamed objects!)

* **Overloading the move assignment operator**:

  ```plain
  Type& Type::operator=(Type &&rhs);
                             --
                             to tell the compiler that the right-side object is an R-value
                             so the right-side value will be an R-value reference
  ```
  Note that the RHS object CANNOT be `const` since we'll be modifying that object when we move the data. (i.e., Nullifying the pointer)

  Example:
  ```cpp
  Mystring& Mystring::operator=(Mystring &&rhs);
  s1 = Mystring("Joe");     // move operator= called
  s1 = "Frank"              // move operator= called
  ```
  ```cpp
  Mystring &Mystring::operator=(Mystring &&rhs)
  {
      // check for self assignment
      if (this == &rhs)     // self assignment
          return *this;     // return current object
  
      delete[] str;         // deallocate current storage
      str = rhs.str;        // steal the pointer (NOT a deep copy)
      rhs.str = nullptr;    // null out the rhs object (to prevent memory leak)
      return *this;         // return current object
  }
  ```
  Very similar to the copy assignment operator except that with move assignment operator we are NOT doing the deep copy. Instead, we're simply stealing the pointer and then nulling out the RHS pointer. Much more efficient (less overhead) than copy assignment.
  
* **Test driver:**
  
  See *Complete `Mystring` Class* secction for the complete class implementation. 
  
  ```cpp
  #include <iostream>
  #include "Mystring.h"
  
  int main(int argc, char *argv[])
  {
      Mystring a("Hello");      // overloaded (parameterized) constructor
      a = Mystring("Hola");     // overloaded constructor then move assignment
                                // - a temporary unnamed object gets created with "Hola"
                                //   and destroyed after move assignment operation is done
      a = "Bonjour"             // overloaded constructor then move assignment
                                // - a temporary unnamed object gets created with
                                //   "Bonjour" and destroyed after move assignment
                                //   operation is done
      return 0;
  }
  ```
  
* If the move constructor, move assignment operator are not defined, this code will invoke copy constructor (or the copy assignment operator) instead.




## Overloading Operators as Member Functions

* C++ allows us to overload operators as 
    - Member functions
    - Global non-member functions

  Here, let's take a look at overloading operators as member functions.
  
* **Overloading unary operators as member functions (`++`, `--`, `-`, `!`):**
  
  Unary operators work on one operand.
  
  ```plain
  ReturnType Type::operatorOp();
                           --
                           operator goes here!
  ```
  > In the case the we have to return a new object from the method, we'll return the new object by value.
  
  Example:
  
  ```cpp
  Number Number::operator-() const;     
  Number Number::operator++();          // pre-increment
  Number Number::operator++(int);       // post-increment
  bool Number::operator!() const;
  
  Number n1(100);
  Number n2 = -n1;      // n1.operator-()
  n2 = ++n1;            // n1.operator++()
  n2 = n1++;            // n1.operator++(int)
  ```
  > Notice that unary member functions have an empty parameter list. This is because the object we're working with is referred to by the `this` pointer. Also, the keyword `int` is used in the parameters for the post-increment to differentiate it from the pre-increment.
  
  Example:
  
  ```cpp
  Mystring jack1{"JACK"};
  Mystring jack2;
  
  jack1.display();	// JACK
  jack2 = -jack1;		// jack1.operator-()
  
  jack1.display();	// JACK
  jack2.display();	// jack
  ```
  
  > Note what the negation operator `-` does when used with a string object.
  
* **Overloading binary operators as member functions (`+`, `-`, `==`, `!=`, `<`, `>`, etc.):**
  
  ```plain
  ReturnType Type::operatorOp(const Type &rhs);
                           --
                           operator goes here!
  ```
  Example:
  ```cpp
  Number Number::operator+(const Number &rhs) const;
  Number Number::operator-(const Number &rhs) const;
  bool Number::operator==(const Number &rhs) const;
  bool Number::operator<(const Number &rhs) const;
  
  Number n1(100), n2(200);
  Number n3 = n1 + n2;      // n1.operator+(n2)
  n3 = n1 - n2;             // n1.operator-(n2)
  if (n1 == n2) . . .       // n1.operator==(n2)
  ```
  Now we have a single parameter in the method parameter list. Of course the `this` pointer points to the LHS operand.

  Implementation of `operator==`:
  ```cpp
  bool Mystring::operator==(const Mystring &rhs) const
  {
      if (std::strcmp(str, rhs.str) == 0)   // <cstring>
          return true;
      else
          return false;
  }
  
  // if (s1 == s2)      // s1 and s2 are Mystring objects
  ```
  Implementation of `operator-`: (make lowercase)
  ```cpp
  Mystring Mystring::operator-() const
  {
      char *buff = new char[std::strlen(str) + 1];	// Allocate memory space
      std::strcpy(buff, str);							// Copy the string
      for (size_t i = 0; std::strlen(buff); i++)		// Make each character lowercase
          buff[i] = std::tolower(buff[i]);			// tolower() is in <ccpyte> header file
      Mystring temp(buff);	// Create a Mystring obj using lowercase string as the initializer
      delete[] buff;			// Delete the buffer created to prevent leak memory
      return temp;			// Return the newly created Mystring object
  }
  ```
  Implementation of `operator+`: (concatenation)
  ```cpp
  Mystring Mystring::operator+(const Mystring &rhs) const
  {
      size_t buff_size = std::strlen(str) + std::strlen(rhs.str) + 1;
      char *buff = new char[buff_size];	// Allocate large enough memory space for two strings
      std::strcpy(buff, str);
      std::strcat(buff, rhs.str);
      Mystring temp(buff);
      delete[] buff;
      return temp;
  }
  ```
  ```cpp
  Mystring larry("Larry");
  Mystring moe("Moe");
  Mystring stooges(" is one of the three stooges");
  
  Mystring result = larry + stooges;    // larry.operator+(stooges);
  result = moe + " is also a stooge";   // moe.operator+("is also a stooge");
  
  result = "Moe" + stooges;             // Illegal! "Meo".operator+(stooges) 
                                        //          -----
                                        //   not an obj of the class this fcn is called on
  ```
  > Notice that we can also use c-style strings on the right-hand side. This is because we have a mystring constructor that can construct mystring objects from a c-style string.
  
* The only limitation to overloading operators as member functions is that the object on the left-hand side must be an object of the class you are using.




## Overloading Operators as Global Functions

* These are no longer member functions, so we do not have `this` pointer referring to the object on the left-hand side.
* Since we very often need access to private attributes in the objects, these non-member functions are often declared as friend functions of the class in many applications. (This isn't absolutely necessary since we can still use getter functions to access attribute values.)
* When you have both the member and non-member versions of an overloaded operator at the same time, make sure that the cases in which they are applied are distinctive. (e.g., Applicable operand types are different, etc.) Otherwise, the compiler wouldn't know which one to use and would throw an error or warning upon encountering one.
* **Overloading unary operators as global functions(`++`, `--`, `-`, `!`):**
  
  ```plain
  ReturnType operatorOp(Type &obj)
                     --
                     operator goes here!
  ```
  Example:
  ```cpp
  Number operator-(const Number &obj);
  Number operator++(Number &obj);               // pre-increment
  Number operator++(Number &obj, int);          // post-increment
  bool   operator!(const Number &obj);
  
  Number n1(100);
  Number n2 = -n1;      // operator-(n1)
  n2 = ++n1;            // operator++(n1)
  n2 = n1++;            // operator++(n1, int)
  ```
  > In the case of the unary operators, a single object is in the parameter list.
  > In the case of the binary operators, two objects are in the parameter list.    
  
* Implementation of operator `-` as a global function: (make lowercase)
  It is assumed that this function has been declared as a friend to the `Mystring` class.
  
  ```cpp
  Mystring operator-(const Mystring &obj)
  {
      char *buff = new char[std::strlen(obj.str) + 1];
      std::strcpy(buff, obj.str);
      for (size_t i = 0; i < std::strlen(buff); i++)
          buff[i] = std::tolower(buff[i]);
      Mystring temp(buff);
      delete[] buff;
      return temp;
  }
  ```
  You CANNOT have both the member and non-member versions of this overloaded operator at the same time. Or the compiler wouldn't know which one to use. You can only have one or the other.
  
* **Overloading binary operators as global functions (`+`, `-`, `==`, `!=`, `<`, `>`, etc.):**
  
  ```plain
  ReturnType operatorOp(const Type &lhs, const Type &rhs);
                     --
                     operator goes here!
  ```
  Example:
  ```cpp
  Number operator+(const Number &lhs, const Number &rhs);
  Number operator-(const Number &lhs, const Number &rhs);
  bool operator==(const Number &lhs, const Number &rhs);
  bool operator<(const Number &lhs, const Number &rhs);
  
  Number n1(100), n2(200);
  Number n3 = n1 + n2;          // operator+(n1, n2)
  n3 = n1 - n2:                 // operator-(n1, n2)
  if (n1 == n2) ...             // operator==(n1, n2)
  ```
  Implementation of `operator==`:
  ```cpp
  bool operator==(const Mystring &lhs, const Mystring &rhs)
  {
      if (std::strcmp(lhs.str, rhs.str) == 0)
          return true;
      else
          return false;
  }
  ```
  > If declared as a friend of `Mystring`, it can access a private attribute `str`. Otherwise a getter function must be used.
  >
  > The code is almost the same as it was for the member version except that now it has an LHS object instead of the `this` pointer.
  
  Implementation of `operator+` (concatenation):
  
  ```cpp
  Mystring operator+(const Mystring &lhs, const Mystring &rhs)
  {
      size_t buff_size = std::strlen(lhs.str) + std::strlen(rhs.str) + 1;
      char *buff = new char[buff_size];
      std::strcpy(buff, lhs.str);
      std::strcat(buff, rhs.str);
      Mystring temp(buff);
      delete[] buff;
      return temp;
  }
  Mystring larry("Larry");
  Mystring moe("Moe");
  Mystring stooges(" is one of the three stooges");
  
  Mystring result = larry + stooges;    // larry.operator+(stooges);
  result = moe + " is also a stooge";   // moe.operator+("is also a stooge");
  
  result = "Moe" + stooges;             // OK with non-member functions
  ```

​		Notice that now the very last line of code is legal with the global (non-member)   overloading function.

* **Test driver:**
  
  ```cpp
  #include <iostream>
  #include <vector>
  #include "Mystrin.h"
  
  using namespace std;
  
  int main(int argc, char *argv[])
  {
      Mystring larry("Larry");
      larry.display();          // Larry
  
      larry = -larry;
      larry.display();          // larry
  
      cout << boolalpha << endl;
      Mystring moe("Moe");
      Mystring stooge = larry;
  
      cout << (larry == moe) << endl;       // false
      cout << (larry == stooge) << endl;    // true
  
      //Mystring stooges = larry + "Moe";
      Mystring stooges = "Larry" + moe;     // Legal with non-member function
      stooges.display();                    // LarryMoe
  
      Mystring two_stooges = moe + " " + Larry;
      two_stooges.display();                // Moe Larry
      Mystring three_stooges = moe + " " + larry + " " + "Curly";
      three_stooges.display();              // Moe larry Curly
  
      return 0;
  }
  ```




## Overloading Stream Insertion and Extraction Operators (`<<`, `>>`)

* This will allow us to insert/extract our `Mystring` objects to/from streams. (This makes our classes feel and behave more like a built-in C++ type.)

* Doesn't make sense to implement as member functions.
  
    Overloading operators as member functions requires left operand to be a user-defined class but this is not the way insertion/extraction operators are normally used.
    
    ```cpp
    Mystring jack;
    jack << cout;		// Hun? Not consistent with how << is used with C++ built-in types
    ```
    
    ```cpp
    Mystring jack;
    cout << jack;		// This is the way << is used with C++ built-in types
    ```
    
* Makes more sense to overload these operators as global functions!

* **Overloading stream insertion operator:**
  
  ```cpp
  std::stream& operator<<(std::ostream &os, const Mystring &obj)
  {
      os << obj.str;        // if friend function
      //os << obj.get_str();  // if not friend function, then must use the public getter function
      return os;
  }
  ```
  > Return a reference to the `ostream` (i.e., output stream reference) so we can keep inserting. Don't return `ostream` by value! (This will incur unnecessary copying of the stream.)

* **Overloading stream insertion operator:**
  
  ```cpp
  std::istream& operator>>(std::istream &is, Mystring &obj)
  {
      char *buff = new char[1000];
      is >> buff;
      obj = Mystring(buff);     // if copy or move assignment are predefined
      delete[] buffer;
      return is;
  }
  ```
  > We want to modify the passed object so this function should NOT be `const`.
  >
  > Update the object passed in.
  >
  > Return a reference to the `istream` so we can do chain insert. 
  >
  > Depending on the data we want to read, we can get the data from the input stream and either store it locally or store it directly in the object.
  
* **Test driver:**
  
  ```cpp
  #include <iostream>
  #include "Mystrin.h"
  
  using namespace std;
  
  int main(int argc, char *argv[])
  {
      Mystring larry("Larry");
      Mystring moe("Moe");
      Mystring curly;
  
      cout << "Enter the third stooge's first name: ";
      cin >> curly;
  
      cout << "The three stooges are " << larry << " " << moe << " " << curly << endl;
  
      cout << "\nEnter the three stooges names separated by a space: ";
      cin >> larry >> moe >> curly;
  
      cout << "The three stooges are " << larry << " " << moe << " " << curly << endl;
  
      return 0;
  }
  ```



## Complete `Mystring` Class

`==`, `-`, `+` oerators are overloaded as both member and non-member functions. As this can cause some ambiguity issues, it would be safe to test (or comment out) one group at a time.

**`Mystring` class declaration:**

```cpp
#ifndef MYSTRING_H
#define MYSTRING_H

class Mystring
{
    friend bool operator==(const Mystring &lhs, const Mystring &rhs);
    friend Mystring operator-(const Mystring &obj);
    friend Mystring operator+(const Mystring &lhs, const Mystring &rhs);
    friend std::ostream& operator<<(std::ostream &os, const Mystring &rhs);
    friend std::istream& operator>>(std::istream &is, Mystring &rhs);

private:
    char *str;    // pointer to a char[] that holds a C-style string

public:
    Mystring();                               // default constructor
    Mystring(const char *s);                  // parameterized constructor
    Mystring(const Mystring &source);         // copy constructor
    Mystring(Mystring &&source);              // move constructor
    ~Mystring();                              // destructor

    Mystring& operator=(const Mystring &rhs); // copy assignment operator overloading
    Mystring& operator=(Mystring &&rhs);      // move assignment operator overloading

    Mystring operator-() const;                       // make lowercase
    Mystring operator+(const Mystring &rhs) const;    // concatenate
    bool operator==(const Mystring &rhs) const;       // equality operator

    void display() const;
    int get_length() const;                   // getter
    const char* get_str() const;              // getter
};

#endif // MYSTRING_H
```

**`Mystring` class implementation:**

```cpp
#include <cstring>    // behind the scenes, Mystring class uses c-string library
#include <iostream>
#include "Mystring.h"

// default constructor
Mystring::Mystring()
    :str(nullptr)
{
    str = new char[1];
    *str = '\0';
}

// parameterized constructor
Mystring::Mystring(const char *s)
    :str(nullptr)
{
    // if an empty string has been passed, create an empty string ""
    if (s == nullptr)
    {
        str = new char[1];
        *str = '\0';
    }
    else
    {
        str = new char[std::strlen(s) + 1];
        std::strcpy(str, s);
    }
}

// copy constructor
Mystring::Mystring(const Mystring &source)
    : str(nullptr)
{
    str = new char[std::strlen(source.str) + 1];
    std::strcpy(str, source.str);
}

// move constructor
Mystring::Mystring(Mystring &&source)
    :str(source.str)          // stealing the pointer of the source object
{
    source.str = nullptr;
}

// destructor
Mystring::~Mystring()
{
    delete[] str;
}

// copy assignment operator
Mystring& Mystring::operator=(const Mystring &rhs)
{
    // check for self assignment
    if (this == &rhs)
        return *this;     // if so, return current object

    delete[] str;         // deallocate storage for 'this->str' since we are overwriting it
                          // delete[] this->str; also works
    str = new char[std::strlen(rhs.str) + 1];  // allocate storage for the deep copy
    std::strcpy(str, rhs.str);    // perform the copy

    return *this;     // return the LHS object by reference to allow chain assignment
}

// move assignment operator
Mystring& Mystring::operator=(Mystring &&rhs)
{
    // check for self assignment
    if (this == &rhs)     // self assignment
        return *this;     // return current object

    delete[] str;         // deallocate current storage
    str = rhs.str;        // steal the pointer (NOT a deep copy)
    rhs.str = nullptr;    // null out the rhs object (to prevent memory leak)
    return *this;         // return current object
}

// equality
bool Mystring::operator==(const Mystring &rhs) const
{
    return (std::strcmp(str, rhs.str) == 0); 
}

// make lowercase
Mystring Mystring::operator-() const
{
    char *buff = new char[std::strlen(str) + 1];
    std::strcpy(buff, str);
    for (size_t i = 0; std::strlen(buff); i++)
        buff[i] = std::tolower(buff[i]);
    Mystring temp(buff);
    delete[] buff;
    return temp;
}

// concatenate
Mystring Mystring::operator+(const Mystring &rhs) const
{
    char *buff = new char[std::strlen(str) + std::strlen(rhs.str) + 1];
    std::strcpy(buff, str);
    std::strcat(buff, rhs.str);
    Mystring temp(buff);
    delete[] buff;
    return temp;
}

// display method
void Mystring::display() const
{
    std::cout << str << ":" << get_length() << std::endl;
}

// length getter
int Mystring::get_length() const
{
    return std::strlen(str);
}

// string getter
const char* Mystring::get_str() const
{
    return str;
}

// equality (global)
bool operator==(const Mystring &lhs, const Mystring &rhs)
{
    return (std::strcmp(lhs.str, rhs.str) == 0);

}

// make lowercase
Mystring operator-(const Mystring &obj)
{
    char *buff = new char[strlen(obj.str) + 1];
    std::strcpy(buff, obj.str);
    for (size_t i = 0; i < std::strlen(buff); i++)
        buff[i] = tolower(buff[i]);
    Mystring temp(buff);
    delete[] buff;
    return temp;
}

// concatenation
Mystring operator+(const Mystring &lhs, const Mystring &rhs)
{
    char *buff = new char[std::strlen(lhs.str) + std::strlen(rhs.str) + 1];
    std::strcpy(buff, lhs.str);
    std::strcat(buff, rhs.str);
    Mystring temp(buff);
    delete[] buff;
    return temp;
}

// stream insertion operator
std::ostream& operator<<(std::ostream &os, const Mystring &obj)
{
    os << obj.str;        // if friend function
    //os << obj.get_str();  // if not friend function
    return os;
}

// stream extraction operator
std::istream& operator>>(std::istream &is, Mystring &obj)
{
    char *buff = new char[1000];
    is >> buff;
    obj = Mystring(buff); // since we have efficient move assignemt, it will be called
                          // (steal the pointer)
    delete[] buff;
    return is;
}
```





## References

Mitropoulos, F. (2022). *Beginning C++ Programming - From Beginner to Beyond* [Video file]. Retrieved from  https://www.udemy.com/course/beginning-c-plus-plus-programming/