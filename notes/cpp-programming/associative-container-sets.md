[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Associative Container - `std::set`, `std::multiset`, `std::unordered_set`, `std::unordered_multiset`

# Associative Container - `std::set`, `std::multiset`, `std::unordered_set`, `std::unordered_multiset`



## The STL Sets

### Associative Containers

* Collections of stored objects that allow fast retrieval using a **key**.
* The STL provides **sets** and **maps** for this purpose.
* Typically implemented as a balanced binary search tree (e.g., red-black tree) or a hash table.
* Most operations are very efficient.

### 4 Types of Sets

* `std::set` - By far the most widely used of all types of sets.
* `std::unordered_set`
* `std::multiset`
* `std::unordered_multiset`



## `std::set`

* Declared in the `<set>` header file.

* Similar concept to a mathematical set.
* Ordered by key.
* Duplicate elements are NOT allowed.
* Does not allow direct access to the elements. (`[]` and `at()` not supported.)
* All iterators are available but may become invalid when the corresponding element is deleted.

### Initialization and Assignment

```cpp
// Initialization
std::set<int> s1{1, 2, 3, 4, 5};

// Initialization
std::set<std::string> s2{
    std::string{"Kyungjae"},
    "Sunny",	// C-style string will be converted to a std::string.
    std::string{"Yena"}
};

// Assignment via initializer list
s1 = {2, 4, 6, 8, 10};
```

### Common Methods

Sets have no concept of front and back. Also, they don't allow direct access to the elements using `[]` or `at()`.

For more information, see [cppreference.com](https://en.cppreference.com/w/).

```cpp
std::set<int> s{4, 1, 1, 3, 3, 2, 5};	// 1, 2, 3, 4, 5
	// This will not cause an error. The duplicates are simply ignored
	// and we end up with a proper set which is also ordered.

std::cout << s.size();		// 5
std::cout << s.max_size();	// A very large number

s.insert(7);				// 1, 2, 3, 4, 5, 7 - added in order
```

```cpp
person p1{"Kyungjae", 30};
person p2{"Sunny", 20};
std::set<person> s;

s.insert(p1);				// adds p1 to the set if it's not present in the set
auto result = s.insert(p2);	// adds p2 to the set if it's not present in the set
```

> L6: `std::set` uses an overloaded `operator<` for ordering, and returns a `std::pair<iterator, bool>`:
>
> * `first` is an interator to the inserted element or to the duplicate element already in the set.
> * `second` is a boolean indicating success or failure.

```cpp
std::set<int> s{1, 2, 3, 4, 5};

s.erase(3);			// erase 3 - 1, 2, 4, 5

auto it = s.find(5);
if (it != s.end())
{
    s.erase(it);	// erase 5 - 1, 2, 4
}

int n = s.count(1);	// 1 (0 or 1 based on the presence of the element)
s.clear();			// remove all elements
s.empty();			// true (true or false depending on the emptiness)
```

> L5: Notice that the `std::set`'s `find()` method is different from the `std::find()` method in the STL `<algorithm>` library. You should use `std::set`'s `find()` because it knows all about the internal implementation of the `std::set`, and it's going to be much more efficient.



## `std::multiset`

* Also declared in the `<set>` header file.
* Ordered by key.
* Duplicate elements are allowed.
* Does not allow direct access to the elements. (`[]` and `at()` not supported.)
* All iterators are available. 



## `std::unordered_set`

* Declared in the `<unordered_set>` header file.
* Elements are **unordered**.
* Duplicate elements are NOT allowed.
* Elements cannot be modified directly in place and they must be erased and re-inserted. (This is due to the way that `std::unordered_set` is implemented.)
* Reverse iterators are not supported.



## `std::unordered_multiset`

* Declared in the `<unordered_set>` header file.
* Elements are **unordered**.
* Duplicate elements are allowed.
* Reverse iterators are not supported.



## Project: Usage of `std::set`

Ensure that your custom classes provide the following three elements to work correctly with the STL:

* Overloaded default constructor
* Overloaded `operator<`
* Overloaded `operator==`

```cpp
#include <iostream>
#include <set>

class person
{
public:
    person() : name{"Unkown"}, age{0} {}    // will be useful when resizing happens
    person(std::string name, int age)
        : name{name}, age{age}
    {}
    bool operator<(const person &rhs) const
    {
        return this->age < rhs.age;
    }
    bool operator==(const person &rhs) const 
    {
        return (this->name == rhs.name && this->age == rhs.age);
    }
    
private:
    friend std::ostream& operator<<(std::ostream &os, const person &p);
    std::string name;
    int age;
};

// Overloading the insertion operator as a global function.
std::ostream& operator<<(std::ostream &os, const person &p)
{
    os << p.name << ":" << p.age;
    return os;
}

// Template function to print elements of any list.
template <typename T>
void print(const std::set<T> &s)
{
    std::cout << "[ ";
    for (const auto &elem : s)
    {
        std::cout << elem << " ";
    }
    std::cout << "]" << std::endl;
}

// insert(), count(), find(), cleear()
void test1(void)
{
    std::cout << "\nTEST1" << std::endl;

    std::set<int> s{1, 4, 3, 5, 2};
    print(s);

    s = {1, 2, 3, 1, 1, 2, 2, 3, 3, 4, 5};  // duplicates will be ignored
    print(s);

    s.insert(0);
    s.insert(10);
    print(s);

    if (s.count(10))
    {
        std::cout << "10 is in the set." << std::endl;
    }
    else
    {
        std::cout << "10 is NOT in the set." << std::endl;
    }

    auto it = s.find(5);    // this is std::set's find(), not the std::find()
    if (it != s.end())
    {
        std::cout << "Found: " << *it << std::endl;
    }

    s.clear();
}

// emplace(), find(), erase(), 
void test2(void)
{
    std::cout << "\nTEST2" << std::endl;

    std::set<person> s{
        {"Kyungjae", 30},
        {"Sunny", 20},
        {"Yena", 5}
    };
    print(s);   // the order is determined by the overloaded 'operator<'

    s.emplace("Nina", 1);
    print(s);

    s.emplace("Theo", 1);    // NO! 1 already exists (uses 'operator<')
    print(s);

    auto it = s.find(person{"Sunny", 20});
    if (it != s.end())
    {
        s.erase(it);
    }
    print(s);

    it = s.find(person("XXX", 1)); // will remove Nina (uses 'operator<')

    if (it != s.end())
    {
        s.erase(it);
    }
    print(s);
}

// Shows what happens when an element is inserted into a set.
void test3(void)
{
    std::cout << "\nTEST3" << std::endl;

    std::set<std::string> s{"A", "B", "C"};
    print(s);

    // Successful insertion
    auto result = s.insert("D");    // result - std::pair<iterator, bool>
    print(s);
    std::cout << std::boolalpha;
    std::cout << "first: " << *(result.first) << std::endl;
    std::cout << "second: " << result.second << std::endl;
    std::cout << std::endl;

    // Failed insertion
    result = s.insert("A");         // result - std::pair<iterator, bool>
    print(s);
    std::cout << std::boolalpha;
    std::cout << "first: " << *(result.first) << std::endl;
    std::cout << "second: " << result.second << std::endl;
}

int main(int argc, char *argv[])
{
    test1();
    test2();
    test3();
    return 0;
}
```

```plain

TEST1
[ 1 2 3 4 5 ]
[ 1 2 3 4 5 ]
[ 0 1 2 3 4 5 10 ]
10 is in the set.
Found: 5

TEST2
[ Yena:5 Sunny:20 Kyungjae:30 ]
[ Nina:1 Yena:5 Sunny:20 Kyungjae:30 ]
[ Nina:1 Yena:5 Sunny:20 Kyungjae:30 ]
[ Nina:1 Yena:5 Kyungjae:30 ]
[ Yena:5 Kyungjae:30 ]

TEST3
[ A B C ]
[ A B C D ]
first: D
second: true

[ A B C D ]
first: A
second: false
```
