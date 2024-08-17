[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Characters & Strings

# Characters & Strings



## Overview

* Character functions (`#include <cctype>`)
* C-style strings
* Working with C-style strings
* **C++ strings (Should be using these in modern C++)**
  * C++ strings are objects and used in OOPs.
* Working with C++ strings



## Character Functions

* The `<cctype>` library includes very simple and very useful functions that allow

  ```cpp
  #include <cctype>
  
  function_name(char)
  ```

* **Testing of characters** for various properties (Returns `true` or `false`)

  ```plain
  isalpha(c)			True if c is a letter
  isalnum(c)			True if c is a letter or digit
  isdigit(c)			True if c is a digit
  islower(c)			True if c is lowercase letter
  isprint(c)			True if c is a printable character 
  ispunct(c)			True if c is a punctuation character 
  isupper(c)			True if c is an uppercase letter 
  isupper(c)			True if c is an uppercase letter 
  isspace(c)			True if c is whitespace 
  ```

* **Conversion of characters** (Returns the converted characters)

  ```plain
  tolower(c)			Returns lowercase of c
  toupper(c)			Returns uppercase of c
  ```



## C-style Strings (Null Terminated Strings!)

### Overview

* **Sequence of characters (String variable)**

  * Contiguous in memory
  * Implemented as an array of characters
  * Terminated by a null character (`\0`; character with a value of 0)
  * Referred to as null (or zero) terminated strings

* **String literal**

  * Sequence of characters in double quotes (e.g., "Jack")

  * Constant

  * Terminated with a null character (`\0`)

    Even though a null character is not provided to the end of the string literal, C++ inserts one for us.

### Declaration

* Declaration and initialization

  ```cpp
  char my_name[] {"Jack"};	// Jack\0 (C++ compiler will allocate 5 char for this array)
  char my_name[10] {};		// Null initialized string
  ```

  > ```cpp
  > char my_name[] {"Jack"};	
  > my_name[4] = 'y';	// Problem
  > ```
  >
  > You won't be able to do to add a `y` to make it `Jacky` because the string size is fixed and adding a `y` to the end will not make the string end with a null character. But, the following is okay.
  >
  > ```cpp
  > char my_name[8] {"Jack"};	// Jack\0\0\0\0
  > my_name[4] = 'y';			// OK (Still null terminated)
  > ```
  >
  > ```cpp
  > char my_name[8];			// Uninitialized string (Contains garbage)
  > my_name = "Jack";			// Error (Both 'my_name' and '"Jack"' evaluates to an address)
  > strcpy(my_name, "Jack");	// OK
  > ```

### Libraries

* `<cstring>` library contains many functions that work with C-style strings

  * Copying
  * Concatenation
  * Comparison
  * Searching
  * and many more

  Examples:

  ```cpp
  #include <cstring>
  
  char str[80];
  strcpy(str, "Hello ");	// Copy
  strcat(str, "there");	// Concatenate
  cout << strlen(str);	// 11
  strcmp(str, "Another");	// > 0
  ```

* `<cstdlib>` library contains general purpose functions including functions to convert C-style strings to integer, float, long, etc.



## C++ Strings (`std::string`)

### Overview

* `std::string` is a class in the Standard Template Library (STL) which provides a rich set of methods or functions that allows to manipulate strings easily.

  * `#include <string>`

  * In the `std` namespace

  * Contiguous in memory

  * Dynamic size

  * Work with input and output streams

  * Lots of useful member functions

  * Operators such as `+`, `=`, `<`, `<=`, `>`, `>=`, `+=`, `==`, `!=`, `[]`, ...

  * Can be easily converted to C-style string if needed

    Even though C++ strings are preferred in most cases, sometimes you need to use C-style strings (e.g., interfacing with a library that's been optimized for C-style strings). In this case, you can still use C++ strings and take advantage of them, and when you need to, you can easily convert the C++ string into a C-style string and back again.

  * Safer (Provides bounce check and allows to find errors in the code so they can be fixed before they go into production)

### Declaration

* Declaration and initialization (Unlike C-style strings, C++ strings are always initialized.)

  ```cpp
  #include <string>
  using namespace std;
  
  string s1;				// Empty (Initialized to an empty string; Does not contain garbage)
  string s2 {"Jack"};		// Jack (C-style string will be converted into a C++ string)
  string s3 {s2};			// Jack
  string s4 {"Jack", 3};	// Jac
  string s5 {s3, 0, 2};	// Ja (Starting at index '0', 2 characters)
  string s6 (3, 'x');		// xxx (Note that these are not curly braces)
  ```

### Operations

* **Assignment**

  ```cpp
  string s1;
  s1 = "Jack";
  
  string s2 {"Hello"};
  s2 = s1;	// s2 will contain "Jack"
  ```

* **Concatenation**

  ```cpp
  string s1 = "Hello";
  string s2 = "world";
  
  string s3;
  
  s3 = s1 + " " + s2 + " Jack";	// Hello world Jack 
  s3 = "New" + "sentence";		// Illegal ('+' only works with C++ strings!)
  ```

  > L7: A combination of C++ strings and C-style strings is okay though!

* **Accessing characters using `[]` and `at()` function**

  ```cpp
  string s1;
  string s2 {"Jack"};
  
  cout << s2[0] << endl;		// J
  cout << s2.at(0) << endl;	// J
  
  s2[0] = 'j';	// s2 will contain "jack"
  s2.at(0) = 'p';	// s2 will contain "pack"
  ```

  > `[]` does not provide bounce check, whereas `at()` does.

  ```cpp
  // Reading characters
  
  string s1 {"Jack"};
  for (char c : s1)
      cout << c << " ";
  ```

  ```plain
  J a c k
  ```

  ```cpp
  // Reading characters as integer values
  
  string s1 {"Jack"};
  for (int c : s1)
      cout << c << " ";
  ```

  ```plain
  74 97 99 107 0	// Last character being the ascii value for '\0'
  ```

* **Comparison (`==`, `!=`, `>`, `>=`, `<`, `<=`)**

  The objects are compared character-by-character lexically.

  Can compare:

  * Two `std::string` objects
  * `std::string` object and C-style string literal
  * `std::string` object and C-style string variable

  Examples:

  ```cpp
  string s1 {"Apple"};
  string s2 {"Banana"};
  string s3 {"Kiwi"};
  string s4 {"apple"};
  string s5 {s1};		// Apple
  
  s1 == s5		// True
  s1 == s2		// False
  s1 != s2		// True
  s1 < s2			// True
  s2 > s1			// True
  s4 < s5			// False
  s1 == "Apple"	// True (Here "Apple" is a C-style literal)
  ```

* **Substrings (`substr()`)**

  Extracts a substring from `std::string`.

  ```cpp
  object.substr(start_index, length)
  ```

  Examples:

  ```cpp
  string s1 {"This is test"};
  
  cout << s1.substr(0, 4); 	// This
  cout << s1.substr(5, 2);	// is
  cout << s1.substr(10, 4);	// test
  ```

* **Search (`find()`)**

  Returns the index of a substring in a `std::string`.

  ```cpp
  object.find(search_string)
  ```

  > Check out `rfind()` which performs the search backwards as well!

  Examples:

  ```cpp
  string s1 {"This is a test"};
  
  cout << s1.find("This");	// 0
  cout << s1.find("is");		// 2
  cout << s1.find("test");	// 10
  cout << s1.find('e');		// 11
  cout << s1.find("is", 4);	// 5
  cout << s1.find("xx");		// string::npos (no position information available)
  ```

  > L8: Can check for `string::npos` in an if statement. If true, the string you are searching for wasn't there.

* **Removing characters (`erase()` and `clear()`)**

  Removes a substring of a character from a `std::string`.

  ```cpp
  object.erase(start_index, length)
  ```

  Examples:

  ```cpp
  string s1 {"This is a test"};
  
  cout << s1.erase(0, 5);		// is a test
  cout << s1.erase(5, 4);		// is a
  s1.clear()					// Empties string s1
  ```

* **Other useful functions**

  ```cpp
  string s1 {"Jack"};
  
  cout << s1.length() << endl;	// 4 (Returns the number of chars currently in the string obj)
  
  s1 += " Lee";
  cout << s1 << endl;		// Jack Lee
  ```

* **Input `>>` and `getline()`**

  Reading `std::string` from `cin`.

  ```cpp
  getline(input_stream, dest_string)	// Reads the entire line until '\n'
  getline(input_stream, dest_string, delimiter)	// Delmiter: The char you want to stop reading at
  ```

  > `getline()` does not include the new-line character `\n` (or delimiter), and discards it.

  Examples:

  ```cpp
  string s1;
  cin >> s1;				// Hello there (Only accepts up to the first space)
  
  cout << s1 << endl;		// Hello
  
  getline(cin, s1);		// Read entire line until \n
  cout << s1 << endl;		// Hello there
  
  getline(cin, s1, 'x');	// this isx
  cout << s1 << endl;		// this is
  ```