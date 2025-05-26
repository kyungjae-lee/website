[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Sequence Container - `std::vector`

# Sequence Container - `std::vector`



## `std::vector`

* Declared in the `<vector>` header file.
* Dynamic size
  * Can expand and contract as needed which is handled automatically by the STL.
  * Elements are stored in continguous memory, like an array.
  * As the `std::vector` expands a new larger area in memory is allocated and the current elements are moved to it.
* Direct element access - Constant time: O(1)
* Rapid insertion and deletion at the back - Constant time: O(1)
* Insertion or deletion of elements (elsewhere) - Linear time: O(n)
* All iterators are available but may become invalid.
  * Iterator invalidation can occur when a `std::vector` resizes and allocates new memory to accommodate additional elements.


### Initialization and Assignment

```cpp
// Initialization
std::vector<int> v1{1, 2, 3, 4, 5};
std::vector<int> v2(10, 500);	// ten 500s

// Initialization
std::vector<std::string> v2{
    std::string{"Kyungjae"},
    "Sunny",	// C-style string will be converted to a std::string.
    std::string{"Yena"}
};

// Assignment via initializer list
v1 = {2, 4, 6, 8, 10};
```

### Common Methods

For more information, see [cppreference.com](https://en.cppreference.com/w/).

```cpp
std::vector<int> v1{1, 2, 3, 4, 5};
std::vector<int> v2{10, 20, 30, 40, 50};

std::cout << v1.size();		// 5
std::cout << v1.capacity();	// 5
std::cout << v1.max_size();	// 5
std::cout << v1.at(0);		// 1 (Supports out-of-bounds check)
std::cout << v1[1];			// 2 (Does not support out-of-bounds check)
std::cout << v1.front();	// 1 (Returns reference to the first element)
std::cout << v1.back();		// 5 (Returns reference to the last element)
std::cout << v1.empty();	// 0 (false)
v1.swap(v2);				// Swaps the two vectors

std::sort(v1.begin(), v1.end());

auto it = std::find(v1.begin(), v1.end(), 3);
v1.insert(it, 10);			// 1, 2, 10, 3, 4, 5

it = std::find(v1.begin(), v.end(), 4);
v1.insert(it, v2.begin(), v2.end());
	// 1, 2, 10, 3, 10, 20, 30, 40, 50, 4, 5
```

> `capacity()`: Returns the number of elements the vector can hold before needing to allocate more memory. When this capacity is exceeded, the vector expands dynamically.
>
> `max_size()`: Returns the maximum number of elements a vector can theoretically hold on the current system. This is typically a very large number and depends on system and implementation limits.

```cpp
person p{"Kyungjae", 30};
std::vector<person> v;

v.push_back(p);					// Add p to the back (copy is made)
v.pop_back();					// Remove the last element (p in this case)
v.push_back(person{"Yena", 5});	// Add a temporary object using move semantics
v.emplace_back("Sunny", 20);	// Construct the object in-place. Very efficient!
```

> L4: Remember, all standard container classes store copies of the elements they hold. So in this case, a copy of `p` is made.
>
> L6: Creates a temporary (unnamed) person object and adds it to the vector using move semantics.
>
> L7: Constructs the person object directly in place using the constructor. Very efficient - no moves, no copies. It’s built exactly where it needs to be. Use this!



## Project: Usage of `std::array`

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

class person
{
public:
    person() = default;
    person(std::string name, int age)
        : name{name}, age{age}
    {}
    // When using the STL with your custom class, always ensure that you 
    // overload `operator<` and `operator==`, as they are commonly used by
    // STL algorithms and containers.
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

// Using for_each() and a lambda expression to print elements.
void print1(const std::vector<int> &v)
{
    std::cout << "[ ";
    std::for_each(v.begin(), v.end(), [](int n) { std::cout << n << " "; });
    std::cout << "]" << std::endl;
}

// Template function to print elements of any vector.
template <typename T>
void print2(const std::vector<T> &v)
{
    std::cout << "[ ";
    for (const auto &elem : v)
        std::cout << elem << " ";
    std::cout << "]" << std::endl;
}

void test1(void)
{
    std::cout << "\nTEST1" << std::endl;
    
    std::vector<int> v{1, 2, 3, 4, 5};
    print1(v);

    v = {2, 4, 5, 6};   // assignment using an initialization list
    print2(v);

    std::vector<int> v1(10, 100);   // ten 100s in the vector
        // Note: This is not an initialization list. We are calling the
        // overloaded constructor defined by the std::vector container.
    print2(v1);
}

// size(), max_size(), capacity(), shrink_to_fit(), reserve()
void test2(void)
{
    std::cout << "\nTEST2" << std::endl;

    std::vector<int> v{1, 2, 3, 4, 5};

    print1(v);
    std::cout << "\nv size: " << v.size() << std::endl;
    std::cout << "v max size: " << v.max_size() << std::endl;
    std::cout << "v capacity: " << v.capacity() << std::endl;

    // Typically when a vector exceeds its capacity, it will double its
    // capacity.
    v.push_back(6);
    print1(v);
    std::cout << "\nv size: " << v.size() << std::endl;
    std::cout << "v max size: " << v.max_size() << std::endl;
    std::cout << "v capacity: " << v.capacity() << std::endl;

    // shrink_to_fit() will shrink the amount of storage allocated to exactly
    // the vector size.
    v.shrink_to_fit();  // C++11
    print2(v);
    std::cout << "\nv size: " << v.size() << std::endl;
    std::cout << "v max size: " << v.max_size() << std::endl;
    std::cout << "v capacity: " << v.capacity() << std::endl;

    // reserve() will reserve a specified amount of storage. The capacity will
    // be adjusted to the passed value.
    v.reserve(100);
    print2(v);
    std::cout << "\nv size: " << v.size() << std::endl;
    std::cout << "v max size: " << v.max_size() << std::endl;
    std::cout << "v capacity: " << v.capacity() << std::endl;
}

// [], at()
void test3(void)
{
    std::cout << "\nTEST3" << std::endl;

    std::vector<int> v{1, 2, 3, 4, 5};
    print1(v);

    v[0] = 100;     // does NOT provide out-of-bounds check
    v.at(1) = 200;  // provides out-of-bounds check

    print2(v);
}

// push_back(), emplace_back()
void test4(void)
{
    std::cout << "\nTEST4" << std::endl;
    std::vector<person> v;

    person p1{"Kyungjae", 30};
    print2(v);

    v.push_back(p1);
    print2(v);

    v.push_back(person{"Sunny", 20});   // temporary object, move semantics
    print2(v);
    
    // Pass the argument we would've passed into the constructor. emplace_back()
    // will call the constructor for us and put the element at the back.
    v.emplace_back("Yena", 5);  // very efficient! highly recommended!
    print2(v);
}

// front(), back(), pop_back()
void test5(void)
{
    std::cout << "\nTEST5" << std::endl;

    std::vector<person> v{
        {"Kyungjae", 30},
        {"Sunny", 20},
        {"Yena", 5}
    };

    print2(v);
    std::cout << "\nFront: " << v.front() << std::endl;
    std::cout << "Back: " << v.back() << std::endl;

    v.pop_back();   // removing the last element from a vector is O(1)
    print2(v);
}

// clear(), erase()
void test6(void)
{
    std::cout << "\nTEST6" << std::endl;

    std::vector<int> v{1, 2, 3, 4, 5};
    print1(v);

    v.clear();  // remove all elements
    print1(v);

    v = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};// assignment using initialization list
    print1(v);
    v.erase(v.begin(), v.begin() + 2);  // remove a subset of elements
    print1(v);

    v = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};// assignment using initialization list
    // erase all even numbers
    auto it = v.begin();
    while (it != v.end())
    {
        if (*it % 2 == 0)
        {
            v.erase(it);
        }
        else
        {
            it++;   // only increment if not erased!
        }
    }

    print1(v);
}

// swap()
void test7(void)
{
    std::cout << "\nTEST7" << std::endl;
    
    std::vector<int> v1{1, 2, 3, 4, 5};
    std::vector<int> v2{10, 20, 30, 40, 50};

    print1(v1);
    print1(v2);
    std::cout << std::endl;

    // To use swap, the continers must store data of the same type, but their
    // size can be different.
    v2.swap(v1);
    print1(v1);
    print1(v2);
}

// sort()
void test8(void)
{
    std::cout << "\nTEST8" << std::endl;

    std::vector<int> v{1, 32, 3, 50, 14};

    print1(v);
    std::sort(v.begin(), v.end());
    print1(v);

    // Note that the reverse() can be used to sort a vector in reverse order.
}

// copy(), copy_if(), back_inserter()
void test9(void)
{
    std::cout << "\nTEST9" << std::endl;

    // std::back_inserter constructs a back-insert iterator that inserts new
    // elements at the end of the container it is applied to. It's a special
    // type of output iterator and is very efficient!
    // There's also std::front_inserter, which can be used with containers like
    // std::deque and std::list.

    // Copy one list to another using an iterator and std::back_inserter.

    std::vector<int> v1{1, 2, 3, 4, 5};
    std::vector<int> v2{10, 20};

    print1(v1);
    print1(v2);
    std::cout << std::endl;

    // Copy the entire v1 and insert it to the end of v2.
    std::copy(v1.begin(), v1.end(), std::back_inserter(v2));
    print1(v1);
    print1(v2);
    std::cout << std::endl;

    // copy_if() the eGement is even
    v1 = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    v2 = {10, 20};

    print1(v1);
    print1(v2);
    std::cout << std::endl;

    // Use the following in the coding assessment. You'll get the job!
    std::copy_if(v1.begin(), v1.end(), std::back_inserter(v2),
        [](int n) { return n % 2 == 0; });  // even only!
    print1(v1);
    print1(v2);
}

// transform()
void test10(void)
{
    std::cout << "\nTEST10" << std::endl;

    // Transform over 2 ranges.
    std::vector<int> v1{1, 2, 3, 4, 5};
    std::vector<int> v2{10, 20, 30, 40, 50};
    std::vector<int> v3;
    
    // 1*10, 2*20, 3*30, 4*40, 5*50 and store the results in v3.
    std::transform(v1.begin(), v1.end(), v2.begin(), std::back_inserter(v3),
        [](int n1, int n2) { return n1 * n2; });
    
    print1(v3);    
}

// find(), insert()
void test11(void)
{
    std::cout << "\nTEST11" << std::endl;

    // Insert v2 into v1 before the element 5.
    std::vector<int> v1{1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    std::vector<int> v2{100, 200, 300, 400};
    
    print1(v1);    
    print1(v2);    
    std::cout << std::endl;

    auto it = std::find(v1.begin(), v1.end(), 5);
    if (it != v1.end())
    {
        std::cout << "Inserting..." << std::endl;
        v1.insert(it, v2.begin(), v2.end());
    }
    else
    {
        std::cout << "Error: 5 not found." << std::endl;
    }

    print1(v1);
}

int main(int argc, char *argv[])
{
    test1();
    test2();
    test3();
    test4();
    test5();
    test6();
    test7();
    test8();
    test9();
    test10();
    test11();
    return 0;
}
```

```plain

TEST1
[ 1 2 3 4 5 ]
[ 2 4 5 6 ]
[ 100 100 100 100 100 100 100 100 100 100 ]

TEST2
[ 1 2 3 4 5 ]

v size: 5
v max size: 2305843009213693951
v capacity: 5
[ 1 2 3 4 5 6 ]

v size: 6
v max size: 2305843009213693951
v capacity: 10
[ 1 2 3 4 5 6 ]

v size: 6
v max size: 2305843009213693951
v capacity: 6
[ 1 2 3 4 5 6 ]

v size: 6
v max size: 2305843009213693951
v capacity: 100

TEST3
[ 1 2 3 4 5 ]
[ 100 200 3 4 5 ]

TEST4
[ ]
[ Kyungjae:30 ]
[ Kyungjae:30 Sunny:20 ]
[ Kyungjae:30 Sunny:20 Yena:5 ]

TEST5
[ Kyungjae:30 Sunny:20 Yena:5 ]

Front: Kyungjae:30
Back: Yena:5
[ Kyungjae:30 Sunny:20 ]

TEST6
[ 1 2 3 4 5 ]
[ ]
[ 1 2 3 4 5 6 7 8 9 10 ]
[ 3 4 5 6 7 8 9 10 ]
[ 1 3 5 7 9 ]

TEST7
[ 1 2 3 4 5 ]
[ 10 20 30 40 50 ]

[ 10 20 30 40 50 ]
[ 1 2 3 4 5 ]

TEST8
[ 1 32 3 50 14 ]
[ 1 3 14 32 50 ]

TEST9
[ 1 2 3 4 5 ]
[ 10 20 ]

[ 1 2 3 4 5 ]
[ 10 20 1 2 3 4 5 ]

[ 1 2 3 4 5 6 7 8 9 10 ]
[ 10 20 ]

[ 1 2 3 4 5 6 7 8 9 10 ]
[ 10 20 2 4 6 8 10 ]

TEST10
[ 10 40 90 160 250 ]

TEST11
[ 1 2 3 4 5 6 7 8 9 10 ]
[ 100 200 300 400 ]

Inserting...
[ 1 2 3 4 100 200 300 400 5 6 7 8 9 10 ]
```
