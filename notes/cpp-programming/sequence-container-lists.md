[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Sequence Container - `std::list`, `std::forward_list`

# Sequence Container - `std::list`, `std::forward_list`



## The STL Lists

* Sequence containers.
* Non-contiguous in memory.
* No direct access to elements using `[]` or `at()`.
* Very efficient for inserting and deleting elements once an element is found.



## `std::list`



<img src="D:/repos/website/notes/cpp-programming/img/list.png" alt="list" width="450">



* Declared in the `<list>` header file.

* Dynamic size
  * **Doubly-linked lists** of elements.
  * Supports bi-directional traverse.
* Direct element access is NOT provided.
* Rapid insertion and deletion of elements anywhere in the container - Constant time: O(1)
  * If you need a container where you'll have  lots of insertions and deletions from the container and you don't need direct access to the elements, then the list is a good choice.
* All iterators are available but may become invalid when the corresponding element is deleted.

### Initialization and Assignment

```cpp
// Initialization
std::list<int> l1{1, 2, 3, 4, 5};
std::list<int> l2(10, 100);	// ten 100s - overloaded constructor

// Initialization
std::list<std::string> l3{
    std::string{"Kyungjae"},
    "Sunny",	// C-style string will be converted to a std::string.
    std::string{"Yena"}
};

// Assignment via initializer list
l1 = {2, 4, 6, 8, 10};
```

### Common Methods

For more information, see [cppreference.com](https://en.cppreference.com/w/).

```cpp
std::list<int> l1{1, 2, 3, 4, 5};
std::list<int> l2{10, 20, 30, 40, 50};

std::cout << l1.size();		// 5
std::cout << l1.max_size();	// A very large number
std::cout << l1.front();	// 1 (Returns reference to the first element)
std::cout << l1.back();		// 5 (Returns reference to the last element)
std::cout << l1.empty();	// 0 (false)

// The following won't work since std::sort() requires random access
// iterators, and std::list provides only bidirectional iterators.
//std::sort(l1.begin(), l1.end());

l1.sort();

auto it = std::find(l1.begin(), l1.end(), 3);
l1.insert(it, 10);	// 1, 2, 10, 3, 4, 5
l1.erase(it);		// erase 3. now 1, 2, 10, 4, 5 ('it' becomes invalid)		

it = std::find(l1.begin(), l1.end(), 4);
l1.insert(it, l2.begin(), l2.end());
    // 1, 2, 10, 10, 20, 30, 40, 50, 4, 5

// Lists are bi-directional, so iterators can iterate in both directions.
std::cout << *it;	// 4
it++;
std::cout << *it; 	// 5
it--;
std::cout << *it; 	// 4

l1.resize(2);		// 1, 2
l1.resize(5);		// 1, 2, 0, 0, 0

l1.swap(l2);				// Swaps the two deques
```

> L17: The list will insert an element very efficiently before 3 and the `it` will still reference 3.

The `std::list` allows for efficiently inserting and deleting elements at the front and back:

```cpp
person p{"Kyungjae", 30};
std::list<person> d;

l.push_back(p);					// Add p to the back (copy is made)
l.pop_back();					// Remove the last element (p in this case)
l.push_front(person{"Sunny", 20});
l.pop_front();					// Remove element from the front
l.emplace_front("Yena", 5);		// Construct the object in-place. Very efficient!
l.emplace_back("Sunny", 20);	// Construct the object in-place. Very efficient!
```



## `std::forward_list` (C++11)



<img src="D:/repos/website/notes/cpp-programming/img/forward_list.png" alt="forward_list" width="450">



* Declared in the `<forward_list>` header file. Introduced in C++11.
* Dynamic size
  * **Singly-linked lists** of elements.
  * Supports uni-directional traverse only. (Does not have the concept of "back".)
  * Less overhead than a `std::list`.
* Direct element access is NOT provided.
* Rapid insertion and deletion of elements anywhere in the container - Constant time: O(1)
  * If you need a container where you'll have  lots of insertions and deletions from the container and you don't need direct access to the elements, then the list is a good choice.
* Reverse iterators are not available.
* Iterators may become invalid when the corresponding element is deleted.

### Initialization and Assignment

```cpp
// Initialization
std::deque<int> d1{1, 2, 3, 4, 5};
std::deque<int> d2(10, 100);	// ten 100s

// Initialization
std::deque<std::string> d3{
    std::string{"Kyungjae"},
    "Sunny",	// C-style string will be converted to a std::string.
    std::string{"Yena"}
};

// Assignment via initializer list
d1 = {2, 4, 6, 8, 10};
```

### Common Methods

For more information, see [cppreference.com](https://en.cppreference.com/w/).

```cpp
std::list<int> l1{1, 2, 3, 4, 5};
std::list<int> l2{10, 20, 30, 40, 50};

//std::cout << l1.size();		// size() not supported
std::cout << l1.max_size();	// A very large number
std::cout << l1.front();	// 1 (Returns reference to the first element)
//std::cout << l1.back();		// back() not supported
std::cout << l1.empty();	// 0 (false)

// The following won't work since std::sort() requires random access
// iterators, and std::list provides only bidirectional iterators.
//std::sort(l1.begin(), l1.end());

l1.sort();

auto it = std::find(l1.begin(), l1.end(), 3);
l1.insert(it, 10);	// 1, 2, 10, 3, 4, 5
l1.erase(it);		// erase 3. now 1, 2, 10, 4, 5 ('it' becomes invalid)		

it = std::find(l1.begin(), l1.end(), 4);
l1.insert_after(it, 10);	// 1, 2, 3, 10, 4, 10, 5
l1.emplace_after(it, 100);	// 1, 2, 3, 10, 4, 100, 10, 5
l1.erase_after(it);			// Erase the 100. 1, 2, 3, 10, 4, 10, 5

// Lists are bi-directional, so iterators can iterate in both directions.
std::cout << *it;	// 4
it++;
std::cout << *it; 	// 5
//it--;				// Not supported

l1.resize(2);		// 1, 2
l1.resize(5);		// 1, 2, 0, 0, 0

l1.swap(l2);				// Swaps the two deques
```

> `max_size()`: Returns the maximum number of elements a vector can theoretically hold on the current 
>
> system. This is typically a very large number and depends on system and implementation limits.

```cpp
person p{"Kyungjae", 30};
std::deque<person> d;

//d.push_back(p);					// Not supported
//d.pop_back();						// Not supported
d.push_front(person{"Sunny", 20});
d.pop_front();					// Remove element from the front
d.emplace_front("Yena", 5);		// Construct the object in-place. Very efficient!
//d.emplace_back("Sunny", 20);		// Not supported
```

> L6: Creates a temporary (unnamed) person object and adds it to the forward list using move semantics.
>
> L8: Constructs the person object directly in place using the constructor. Very efficient - no moves, no copies. It’s built exactly where it needs to be. Use this!



## Project: Usage of `std::list`

Ensure that your custom classes provide the following three elements to work correctly with the STL:

* Overloaded default constructor
* Overloaded `operator<`
* Overloaded `operator==`

`test3()` in the following code demonstrates the importance of defining a default constructor for your custom class.

```cpp
#include <iostream>
#include <list>
#include <algorithm>
#include <iterator> // std::advance()

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
void print(const std::list<T> &l)
{
    std::cout << "[ ";
    for (const auto &elem : l)
    {
        std::cout << elem << " ";
    }
    std::cout << "]" << std::endl;
}

// push_front(), push_back()
void test1(void)
{
    std::cout << "\nTEST1" << std::endl;

    std::list<int> l1{1, 2, 3, 4, 5};
    print(l1);

    // Very efficient when inserting at both ends.
    std::list<std::string> l2;
    l2.push_back("Back");
    l2.push_front("Front");
    print(l2);

    std::list<int> l3;
    l3 = {1, 2, 3, 4, 5, 5, 6, 7, 8, 9, 10};
    print(l3);

    std::list<int> l4(10, 100); // ten 100s in the list (using constructor)
    print(l4);
}

// size(), front(), back(), clear()
void test2(void)
{
    std::cout << "\nTEST2" << std::endl;

    std::list<int> l{1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    print(l);

    std::cout << "Size: " << l.size() << std::endl;

    std::cout << "Front: " << l.front() << std::endl;
    std::cout << "Back: " << l.back() << std::endl;

    l.clear();
    print(l);
    std::cout << "Size: " << l.size() << std::endl;
}

void test3(void)
{
    std::cout << "\nTEST3" << std::endl;

    std::list<int> l1{1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    print(l1);

    l1.resize(5);
    print(l1);

    l1.resize(10);  
    print(l1);

    // This shows why it is important to define a overloaded default constructor
    // when using a custom class with the STL. (as well as 'operator<' and 
    // 'operator==')
    std::list<person> l2;
    l2.resize(5);   // uses the person's overloaded default constructor
    print(l2);
}

void test4(void)
{
    std::cout << "\nTEST4" << std::endl;

    std::list<int> l1{1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    print(l1);
    auto it = std::find(l1.begin(), l1.end(), 5);
    if (it != l1.end())
    {
        l1.insert(it, 100); // insert a 100 before the iterator
    }
    print(l1);

    std::list<int> l2{1000, 2000, 3000};
    l2.insert(it, l2.begin(), l2.end());    // 'it' still referencing 5 in l1
    print(l1);  // 1 2 3 4 100 1000 2000 3000 5 6 7 8 9 10

    std::advance(it, -4);   // point to the 100
    std::cout << *it << std::endl;

    l1.erase(it);   // remove the 100 - iterator becomes invalid
                    // to use iterator again you need to reset it!
    print(l1);
}

// emplace(), emplace_back(), find()
void test5(void)
{
    std::cout << "\nTEST5" << std::endl;

    std::list<person> l{
        {"Kyungjae", 30},
        {"Sunny", 20},
        {"Yena", 5}
    };

    print(l);
    std::string name;
    int age{};
    std::cout << "\nEnter the name of the next person: ";
    getline(std::cin, name);
    std::cout << "Enter their age: ";
    std::cin >> age;

    // I'm not creating an object to add to the list. The list will create an
    // object for me based on the passed values.
    l.emplace_back(name, age);
    print(l);

    // Insert Nina before Yena
    auto it = std::find(l.begin(), l.end(), person{"Yena", 5});
    if (it != l.end())
    {
        l.emplace(it, "Nina", 1);
    }
    print(l);
}

// sort()
void test6(void)
{
    std::cout << "\nTEST6" << std::endl;

    std::list<person> l{
        {"Kyungjae", 30},
        {"Sunny", 20},
        {"Yena", 5}
    };

    print(l);
    l.sort();   // sort() will use the overloaded 'operator<' 
    print(l);
}

int main(int argc, char *argv[])
{
    test1();
    test2();
    test3();
    test4();
    test5();
    test6();
    return 0;
}
```

```plain
TEST1
[ 1 2 3 4 5 ]
[ Front Back ]
[ 1 2 3 4 5 5 6 7 8 9 10 ]
[ 100 100 100 100 100 100 100 100 100 100 ]

TEST2
[ 1 2 3 4 5 6 7 8 9 10 ]
Size: 10
Front: 1
Back: 10
[ ]
Size: 0

TEST3
[ 1 2 3 4 5 6 7 8 9 10 ]
[ 1 2 3 4 5 ]
[ 1 2 3 4 5 0 0 0 0 0 ]
[ Unkown:0 Unkown:0 Unkown:0 Unkown:0 Unkown:0 ]

TEST4
[ 1 2 3 4 5 6 7 8 9 10 ]
[ 1 2 3 4 100 5 6 7 8 9 10 ]
[ 1 2 3 4 100 1000 2000 3000 5 6 7 8 9 10 ]
100
[ 1 2 3 4 1000 2000 3000 5 6 7 8 9 10 ]

TEST5
[ Kyungjae:30 Sunny:20 Yena:5 ]

Enter the name of the next person: Miyoung
Enter their age: 50
[ Kyungjae:30 Sunny:20 Yena:5 Miyoung:50 ]
[ Kyungjae:30 Sunny:20 Nina:1 Yena:5 Miyoung:50 ]

TEST6
[ Kyungjae:30 Sunny:20 Yena:5 ]
[ Yena:5 Sunny:20 Kyungjae:30 ]
```
