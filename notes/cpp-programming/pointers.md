[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Pointers

# Pointers



## Overview

* What is a pointer?
* Declaring pointers
* Storing addresses in pointers
* Dereferencing pointers
* Dynamic memory allocation
* Pointer arithmetic
* Pointers and arrays
* Pass-by-reference with pointers
* `const` and pointers
* Using pointers to functions
* Potential pointer pitfalls



## What is a Pointer?

* A variable whose value is an address

* What can be at that address?
  * Another variable
  
  * A function
  
* Pointers point to variables or functions?

* If x is an integer variable and its value is 10, then I can declare a pointer that points to it.

* To use the data that the pointer is pointing to you must know its type



## Why Use Pointers?

* Can't I just use the variable or function itself? $\to$ Yes, but not always!
* Inside functions, pointers can be used to access data that are defined outside the function. Those variables may not be in scope so you can't access them by their name.
* Pointers can be used to operate on arrays very efficiently.
* We can allocate memory dynamically on the heap or free store.
  * This memory doesn't even have a variable name.
  * The only way to get to it is via a pointer.
* With OO, pointers are how polymorphism works!
* Can access specific addresses in memory.
  * Useful in embedded and systems applications



## Declaring Pointers

* Declaration

  ```cpp
  variable_type *pointer_name;
  ```

  Example:

  ```cpp
  int *int_ptr;
  double *double_ptr;
  char *char_ptr;
  string *string_ptr;
  ```

* Initializing pointer variables to 'point nowhere'

  ```cpp
  variable_type *pointer_name {nullptr};
  ```

  Example;

  ```cpp
  int *int_ptr{};
  double *double_ptr{nullptr};
  char *char_ptr{nullptr};
  string *string_ptr{nullptr};
  ```

* Always initialize pointers

* Uninitialized pointers contain garbage data and can 'point anywhere'.

* Initializing to zero or `nullptr` (C++11) represents address zero

  * Implies that the pointer is 'pointing nowhere'

* If you don't initialize a pointer to point to a variable or function then you should initialize it to `nullptr` to 'make it null'.



## Accessing Pointer Address

* `&` - The address operator

  * Variables are stored in unique addresses
  * Unary operator
  * Evaluates to the address of its operand
    * Operand cannot be a constant or expression that evaluates to temp values

* Example

  ```cpp
  int num{10};
  cout << "Value of num is: " << num << endl;			// 10
  cout << "sizeof of num is: " << sizeof num << endl;	// 4
  cout << "Address of num is: " << &num << endl;		// 0x61ff1c
  ```

  ```cpp
  int *p;
  cout << "Value of p is: " << p << endl;			// 0x61ff60 - garbage
  cout << "Address of p is : " << &p << endl;		// 0x61ff18
  cout << "sizeof of p is: " << sizeof p << endl;	// 4
  p = nullptr;	// Set p to point to nowhere
  cout << "Value of p is: " << p << endl;			// 0
  ```



## `sizeof` a Pointer Variable

* Don't confuse the size of a pointer and the size of what it points to.

* All pointers in a program have the same size.

* They may be pointing to very large or very small types.

* Example

  ```cpp
  int *p1{nullptr};
  double *p2{nullptr};
  unsigned long long *p3{nullptr};
  vector<string> *p4{nullptr};
  string *p5{nullptr};
  ```



## Storing an Address in a Pointer Variable (Typed Pointers)

* The compiler will make sure that the address stored in a pointer variable is of the correct type.

  ```cpp
  int score{10};
  double high_temp{100.7};
  
  int *score_ptr{nullptr};
  score_ptr = &score;			// OK
  score_ptr = &high_temp;		// Compiler Error
  ```

* Pointers are variables so they can change (Pointers can be null or uninitialized)

  ```cpp
  double high_temp{100.7};
  double low_temp{37.2};
  
  double *temp_ptr;
  
  temp_ptr = &high_temp;		// Points to high_temp;
  temp_ptr = &low_temp;		// Now points to low_temp;
  
  temp_ptr = nullptr;
  ```



## Dereferencing a Pointer

* Dereferencing a pointer means accessing the data the pointer is pointing to.

* If `score_ptr` is a pointer and has a valid address, then you can access the data at the address contained in the `score_ptr` using the **dereferencing operator `*`**.

  ```cpp
  int score{100};
  int *score_ptr{&score};
  
  cout << *score_ptr << endl;		// 100
  
  *score_ptr = 200;
  cout << *score_ptr << endl;		// 200
  cout << score << endl;			// 200
  ```

  ```cpp
  double high_temp{100.7};
  double low_temp{37.4};
  double *temp_ptr{&high_temp};
  
  cout << *temp_ptr << endl;		// 100.7
  
  temp_ptr = &low_temp;
  	
  cout << *temp_ptr << endl;		37.4
  ```

  ```cpp
  string name{"Jack"};
  string *string_ptr{&name};
  
  cout << *string_ptr << endl;	// Jack
  
  name = "Yena";
  
  cout << *string_ptr << endl;	// Yena
  ```



## Dynamic Memory Allocation (`new` & `delete`)

Allocating storage from the heap at run-time

* We often don't know how much storage we need until we need it.

* We can allocate storage for a variable at run-time

* Recall C++ arrays

  * We had to explicitly provide the size and it was fixed.
  * But, vectors grow and shrink dynamically.

* We can use pointers to access newly allocated heap storage.

* Using `new` to allocate storage

  ```cpp
  int *int_ptr{nullptr};
  int_ptr = new int;			// Allocate an integer on the heap
  cout << int_ptr << endl;	// 0x2747f28
  cout << *int_ptr << endl;	// 41188048 - garbage
  *int_ptr = 100;
  cout << *int_ptr << endl;	// 100
  ```

  Using `delete` to deallocate storage

  ```cpp
  int *int_ptr{nullptr};
  int_ptr = new int;		// Allocate an integer on the heap
  . . .
  delete int_ptr;			// Frees the allocated storage
  ```

* Using `new[]` to allocate storage for an array

  ```cpp
  int *array_ptr{nullptr};
  int size{};
  
  cout << "How big do you want the array?";
  cin >> size;
  
  array_ptr = new int[size];	// Allocate array on the heap
  
  // We can access the array here
  ```

  Using `delete[]` to deallocate storage for an array

  ```cpp
  int *array_ptr{nullptr};
  int size{};
  
  cout << "How big do you want the array?";
  cin >> size;
  
  array_ptr = new int[size];	// Allocate array on the heap
  
  . . .
      
  delete []array_ptr;			// Free Allocated storage
  ```

  

## Relationship between Arrays and Pointers

* The value of an array name is the address of the first element in the array.

* The value of a pointer variable is an address.

* If the pointer points to the same data type as the array element then the pointer and array name can be used in interchangeably (**almost**).

* Example

  ```cpp
  int scores[]{100, 95, 89};
  
  cout << scores << endl;			// 0x61fec8
  cout << *scores << endl;		// 100
  
  int *score_ptr{scores};
  
  cout << score_ptr << endl;		// 0x61fec8
  
  cout << *score_ptr << endl;		// 100
  ```

  ```cpp
  int scores[]{100, 95, 89};
  
  int *score_ptr{scores};
  
  cout << score_ptr[0] << endl;	// 100
  cout << score_ptr[1] << endl;	// 95
  cout << score_ptr[2] << endl;	// 89
  ```

* Using pointers in expressions

  ```cpp
  int scores[]{100, 95, 89};
  
  int *score_ptr{scores};
  
  cout << score_ptr << endl;			// 0x61ff10
  cout << (score_ptr + 1) << endl;	// 0x61ff14
  cout << (score_ptr + 2) << endl;	// 0x61ff18
  ```

  ```cpp
  int scores[]{100, 95, 89};
  
  int *score_ptr{scores};
  
  cout << *score_ptr << endl;			// 100
  cout << *(score_ptr + 1) << endl;	// 95
  cout << *(score_ptr + 2) << endl;	// 89
  ```



## Subscript & Offset Notation Equivalence

* Subscript & Offset notation

  ```cpp
  int array_name[]{1, 2, 3, 4, 5};
  int *pointer_name{array_name};
  ```

  | Subscript Notation  | Offset Notation         |
  | ------------------- | ----------------------- |
  | array_name[index]   | *(array_name + index)   |
  | pointer_name[index] | *(pointer_name + index) |



## Pointer Arithmetic

* Pointers can be used in
  * Assignment expressions
  * Arithmetic expressions
  * Comparison expressions
* C++ allows pointer arithmetic
* Pointer arithmetic only makes sense with raw arrays

* `++` and `--`

  * `++` increments a pointer to point to the next array element

    ```cpp
    int_ptr++;
    ```

  * `--` decrements a pointer to point to the previous array element

    ```cpp
    int_ptr--;
    ```

* `+` and `-`

  * `+` increment pointer by `n * sizeof(type)`

    ```cpp
    int_ptr += n; 	// int_ptr = int_ptr + n;
    ```

  * `-` increment pointer by `n * sizeof(type)`

    ```cpp
    int_ptr -= n; 	// int_ptr = int_ptr - n;
    ```

* Subtracting two pointers

  * Determine the number of elements between the pointers

  * Both pointers must point to the same data type

    ```cpp
    int n = int_ptr2 - int_ptr1;
    ```

* Comparing two pointers (`==` and `!=`)

  Determine if two pointers point to the same location (Does NOT compare the data where they point!)

  ```cpp
  string s1{"Jack"};
  string s2{"Jack"};
  
  string *p1{&s1};
  string *p2{&s2};
  string *p3{&s3};
  
  cout << (p1 == p2) << endl;		// False
  cout << (p1 == p3) << endl;		// True
  ```



## `const` and Pointers

There are several ways to qualify pointers using `const`.

* **Pointers to constants**

  * The data pointed to by the pointers is constant and CANNOT be changed.
  * The pointer itself can change and point somewhere else.

  ```cpp
  int high_score{100};
  int low_score{65};
  const int *score_ptr{&high_score};
  
  *score_ptr = 86;			// ERROR
  score_ptr = &low_score;		// OK
  ```

* **Constant pointers**

  * The data pointed to by the pointers is can be changed.
  * The pointer itself CANNOT change and point somewhere else.

  ```cpp
  int high_score{100};
  int low_score{65};
  int *const score_ptr{&high_score};
  
  *score_ptr = 86;			// OK
  score_ptr = &low_score;		// ERROR
  ```

* **Constant pointers to constants**

  * The data pointed to by the pointers is constant and CANNOT be changed.
  * The pointer itself CANNOT change and point somewhere else.

  ```cpp
  int high_score{100};
  int low_score{65};
  const int *const score_ptr{&high_score};
  
  *score_ptr = 86;			// OK
  score_ptr = &low_score;		// ERROR
  ```

  

## Passing Pointers to a Function

* Pass-by-reference with pointer parameters
* We can use pointers and the dereference operator to achieve pass-by-reference
* The function parameter is a pointer
* The actual parameter can be a pointer or address of a variable

### Example

* Pass-by-reference with pointers

  Defining the function

  ```cpp
  void double_data(int *int_ptr);
  
  void double_data(int *int_ptr);
  {
      *int_ptr *= 2;	// *int_ptr = *int_ptr * 2;
  }
  ```

  Calling the function

  ```cpp
  int main()
  {
      int value{10};
      cout << value << endl;		// 10
      double_data(&value);
      cout << value << endl;		// 20
  }
  ```



## Returning a Pointer from a Function

* Functions can also return pointers

  ```cpp
  type* function();
  ```

* Should return pointers to

  * Memory dynamically allocated in the function
  * To data that was passed in (i.e., parameter)

* Never return a pointer to a local function variable!

### Example

* Returning a paramter

  ```cpp
  int* largest_int(int *int_ptr1, int *int_ptr2)
  {
      return (*int_ptr1 > *int_ptr2) ? int_ptr1 : int_ptr2;
  }
  
  int main(void)
  {
      int a{100};
      int b{200};
      
      int *largest_ptr{nullptr};
      largest_ptr = largest_int(&a, &b);
      cout << *largest_ptr << endl;		// 200
      return 0;
  }
  ```

* Returning dynamically allocated memory

  ```cpp
  int* create_array(size_t size, int init_value = 0)
  {
      int *new_storage{nullptr};
      
      new_storage = new int[size];
      for (size_t i{0}; i < size; ++i)
          *(new_storage + i) = init_value;
      return new_storage;
  }
  
  int main(void)
  {
      int *my_array;	// Will be allocated by the function
      
      my_array = create_array(100, 20);	// Create the array
      
      // Use it
      
      delete []my_array;	// Be sure to free the storage
      return 0;
  }
  ```

* Never return a pointer to a local variable!

  ```cpp
  int* dont_do_this()
  {
      int size{};
      . . .
      return &size;
  }
  
  int* or_this()
  {
      int size{};
      int *int_ptr{&size};
      . . .
      return int_ptr;
  }
  ```

  > Local variables will no longer exist outside of the function body in which they are declared. Thus, the returned pointer will point to a location that doesn't even exist.
  >
  > $\to$ Dangling pointer!



## Potential Pointer Pitfalls

* Uninitialized pointers

  ```cpp
  int *int_ptr; 		// Pointing anywhere
  . . .
  *int_ptr = 100;		// Hopefully a crash
  ```

* Dangling pointers

  * Pointer that is pointing to released memory

    e.g., 2 pointers point to the same data, 1 pointer releases the data with delete, the other pointer accesses the released data

  * Pointer that points to memory that is invalid

    e.g., When a pointer to a local variable in a function is returned

* Not checking if `new` failed to allocate memory

  * If `new` fails an exception is thrown.
  * We can use exception handling to catch exceptions.
  * Dereferencing a null pointer will cause your program to crash

* Memory leak

  * Forgetting to release allocated memory with delete
  * If you lose your pointer to the storage allocated on the heap you don't have way to get to that storage again!
  * The memory is orphaned or leaked
  * One of the most common pointer problems!



## Pointer Parameters vs. Reference Parameters

* Pass-by-value

  * When the function does NOT modify the actual parameter

  * When the parameter is small and efficient to copy like simple types (`int`, `char`, `double`, etc.)

* Pass-by-reference using a pointer

  * When the function does modify the actual parameter
  * When the parameter is expensive to copy
  * It's OK for the pointer to contain a `nullptr` value

* Pass-by-reference using a pointer to `const`

  * When the function does **NOT** modify the actual parameter
  * When the parameter is expensive to copy
  * It's OK for the pointer to contain a `nullptr` value

* Pass-by-reference using a `const` pointer to `const`

  * When the function does **NOT** modify the actual parameter
  * When the parameter is expensive to copy
  * It's OK for the pointer to contain a `nullptr` value
  * When you don't want to modify the pointer itself

* Pass-by-reference using a reference

  * When the function **DOES** modify the actual parameter
  * When the parameter is expensive to copy
  * The parameter will never be `nullptr`

* Pass-by-reference using a `const` reference

  * When the function does **NOT** modify the actual parameter
  * When the parameter is expensive to copy
  * The parameter will never be `nullptr`
