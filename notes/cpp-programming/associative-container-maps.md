[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Associative Container - `std::map`, `std::multimap`, `std::unordered_map`, `std::unordered_multimap`

# Associative Container - `std::map`, `std::multimap`, `std::unordered_map`, `std::unordered_multimap`



## The STL Maps

### Associative Containers

* Collections of stored objects that allow fast retrieval using a **key**.
* The STL provides **sets** and **maps** for this purpose.
* Typically implemented as a balanced binary search tree (e.g., red-black tree) or a hash table.
* Most operations are very efficient.

### 4 Types of Maps

* `std::map` - By far the most widely used of all types of sets.
* `std::unordered_map`
* `std::multimap`
* `std::unordered_multimap`



## `std::map`

* Declared in the `<map>` header file.

* Similar concept to a dictionary.
* Elements are stored as Key-Value pairs (`std::pair`)
* Ordered by key.
* Duplicate elements are NOT allowed. (Keys are unique.)
* Allows direct access to the elements using the key.
* All iterators are available but may become invalid when the corresponding element is deleted.

### Initialization

```cpp
// Initialization
std::map<std::string, int> m1{
    {"Kyungjae", 30},
    {"Sunny", 20}
};

// Initialization
std::map<std::string, std::string> m2{
    {"Kyungjae", "Engineer"},
    {"Sunny", "Designer"},
    {"Yena", "Student"}
};
```

### Common Methods

Like sets, maps do not support the concept of front and back.

For more information, see [cppreference.com](https://en.cppreference.com/w/).

```cpp
std::map<std::string, std::string> m{
    {"Kyungjae", "Engineer"},
    {"Sunny", "Designer"},
    {"Yena", "Student"}
};

std::cout << m.size();			// 3
std::cout << m.max_size();		// A very large number

std::pair<std::string, std::string> p{"Nina", "Baby"};
m.insert(p);

m.insert(std::make_pair("Jaesoo", "Professor"));

m["Hyera"] = "Teacher";			// insert

m["Hyera"] = "Dancer";			// update value
m.at("Hyera") = "Cook";			// update value

m.erase("Sunny");				// erase Sunny

if (m.find("Yena") != m.end())	// find Yena
    std::cout << "Found Yena!";

auto it = m.find("Kyungjae");
if (it != m.end())
    m.erase(it);				// erase Kyungjae

int n = m.count("Nina");		// 1 (0 or 1 based on the presence of the element)
m.clear();						// remove all elements
m.empty();						// true (true or false depending on the emptiness)
```

> L22: Notice that the `std::map`'s `find()` method is different from the `std::find()` method in the STL `<algorithm>` library. You should use `std::map`'s `find()` because it knows all about the internal implementation of the `std::map`, and it's going to be much more efficient.



## `std::multimap`

* Also declared in the `<map>` header file.
* Ordered by key.
* Duplicate elements are allowed.
* Allows direct access to the elements using the key.
* All iterators are available. 



## `std::unordered_map`

* Declared in the `<unordered_map>` header file.
* Elements are **unordered**.
* Duplicate elements are NOT allowed.
* Reverse iterators are not supported.



## `std::unordered_multimap`

* Declared in the `<unordered_map>` header file.
* Elements are **unordered**.
* Duplicate elements are allowed.
* Reverse iterators are not supported.



## Project: Usage of `std::map`

Ensure that your custom classes provide the following three elements to work correctly with the STL:

* Overloaded default constructor
* Overloaded `operator<`
* Overloaded `operator==`

```cpp
#include <iostream>
#include <map>
#include <set>


```

```plain

```
