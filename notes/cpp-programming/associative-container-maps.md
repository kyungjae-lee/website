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

void print(const std::map<std::string, std::set<int>> &m)
{
    std::cout << "[ ";
    for (const auto &m_elem : m)
    {
        std::cout << m_elem.first << ": [ ";
        for (const auto &s_elem : m_elem.second)
        {
            std::cout << s_elem << " ";
        }
        std::cout << "] ";
    }
    std::cout << "]" << std::endl;
}

template <typename T1, typename T2>
void print(const std::map<T1, T2> &m)
{
    std::cout << "[ ";
    for (const auto &elem: m)
    {
        std::cout << elem.first << ":" << elem.second << " ";
    }
    std::cout << "]" << std::endl;
}

// insert(), std::make_pair(), [], count(), find(), clear()
void test1(void)
{
    std::cout << "\nTEST1" << std::endl;

    std::map<std::string, int> m{
        {"Kyungjae", 30},
        {"Sunny", 20},
        {"Yena", 5}
        // elements will be ordered by key; std::string in this case
    };
    print(m);

    m.insert(std::pair<std::string, int>("Nina",1));
    print(m);

    // make_pair() will figure out the types based on the passed values
    m.insert(std::make_pair("Hyera", 50));
    print(m);

    m["Jaesoo"] = 60;
    print(m);

    m["Jaesoo"] += 1;
    print(m);

    m.erase("Jaesoo");
    print(m);

    std::cout << "Count for Hyera: " << m.count("Hyera") << std::endl;
    std::cout << "Count for Jaesoo: " << m.count("Jaesoo") << std::endl;

    auto it = m.find("Nina");
    if (it != m.end())
    {
        std::cout << "Found: " << it->first << ":" << it->second << std::endl;
    }

    m.clear();
    print(m);
}

// insert(), find()
void test2(void)
{
    std::cout << "\nTEST2" << std::endl;

    std::map<std::string, std::set<int>> m{
        {"Kyungjae", {60, 90}},
        {"Sunny", {80}},
        {"Yena", {70, 90, 100}}
        // elements will be ordered by key; std::string in this case
    };
    print(m);

    m["Kyungjae"].insert(85);   // insert 85 into Kyungjae's set
    print(m);

    auto it = m.find("Yena");
    if (it != m.end())
    {
        it->second.insert(1000);    // insert 1000 into Yena's set
    }
    print(m);
}

int main(int argc, char *argv[])
{
    test1();
    test2();
    return 0;
}
```

```plain

TEST1
[ Kyungjae:30 Sunny:20 Yena:5 ]
[ Kyungjae:30 Nina:1 Sunny:20 Yena:5 ]
[ Hyera:50 Kyungjae:30 Nina:1 Sunny:20 Yena:5 ]
[ Hyera:50 Jaesoo:60 Kyungjae:30 Nina:1 Sunny:20 Yena:5 ]
[ Hyera:50 Jaesoo:61 Kyungjae:30 Nina:1 Sunny:20 Yena:5 ]
[ Hyera:50 Kyungjae:30 Nina:1 Sunny:20 Yena:5 ]
Count for Hyera: 1
Count for Jaesoo: 0
Found: Nina:1
[ ]

TEST2
[ Kyungjae: [ 60 90 ] Sunny: [ 80 ] Yena: [ 70 90 100 ] ]
[ Kyungjae: [ 60 85 90 ] Sunny: [ 80 ] Yena: [ 70 90 100 ] ]
[ Kyungjae: [ 60 85 90 ] Sunny: [ 80 ] Yena: [ 70 90 100 1000 ] ]
```
