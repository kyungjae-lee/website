[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Sequence Container - `std::deque`

# Sequence Container - `std::deque`



## `std::deque`

* Declared in the `<deque>` header file.
* Acts like a double-ended queue.
* Dynamic size
  * Can expand and contract as needed which is handled automatically by the STL.
  * Elements are **NOT** stored in continguous memory.
    * Usually, a deque is implemented as a collection of memory blocks, and these memory blocks and these memory blocks contain elements that are in a contiguous memory. However, the blocks themselves are not in contiguous memory.
    * A good way to think of a deque is as a linked list of vectors. So when adding an element at the front, it adds it if there's a space. If not, it will allocate a new block, add the element to that block and then link in the block. (The same happens when adding an element at the back.)
* Direct element access - Constant time: O(1)
* Rapid insertion and deletion at the **front** and **back** - Constant time: O(1)
* Insertion or deletion of elements (elsewhere) - Linear time: O(n)
* All iterators are available but may become invalid.
  * Iterator invalidation can occur when a `std::deque` changes size.



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
std::deque<int> d1{1, 2, 3, 4, 5};
std::deque<int> d2{10, 20, 30, 40, 50};

std::cout << d1.size();		// 5
std::cout << d1.max_size();	// A very large number
std::cout << d1.at(0);		// 1 (Supports out-of-bounds check)
std::cout << d1[1];			// 2 (Does not support out-of-bounds check)
std::cout << d1.front();	// 1 (Returns reference to the first element)
std::cout << d1.back();		// 5 (Returns reference to the last element)
std::cout << d1.empty();	// 0 (false)

std::sort(d1.begin(), d1.end());

auto it = std::find(d1.begin(), d1.end(), 3);
d1.insert(it, 10);			// 1, 2, 10, 3, 4, 5

it = std::find(d1.begin(), d1.end(), 4);
d1.insert(it, d2.begin(), d2.end());
	// 1, 2, 10, 3, 10, 20, 30, 40, 50, 4, 5

d1.swap(d2);				// Swaps the two deques
```

> `max_size()`: Returns the maximum number of elements a vector can theoretically hold on the current 
>
> system. This is typically a very large number and depends on system and implementation limits.

```cpp
person p{"Kyungjae", 30};
std::deque<person> d;

d.push_back(p);					// Add p to the back (copy is made)
d.pop_back();					// Remove the last element (p in this case)
d.push_front(person{"Sunny", 20});
d.pop_front();					// Remove element from the front
d.emplace_front("Yena", 5);		// Construct the object in-place. Very efficient!
d.emplace_back("Sunny", 20);	// Construct the object in-place. Very efficient!
```

> L4: Remember, all standard container classes store copies of the elements they hold. So in this case, a copy of `p` is made.
>
> L6: Creates a temporary (unnamed) person object and adds it to the deque using move semantics.
>
> L8: Constructs the person object directly in place using the constructor. Very efficient - no moves, no copies. It’s built exactly where it needs to be. Use this!



## Project: Usage of `std::deque`

`std::deque` is ideal when you need fast insertions or deletions at **both the front and back** of the container. It is **not optimized for frequent insertions or removals in the middle**. If your use case involves modifying elements in the middle, consider using `std::list` instead.

```cpp
#include <iostream>
#include <deque>
#include <vector>
#include <algorithm>

// Template function to print elements of any deque.
template <typename T>
void print(const std::deque<T> &d)
{
    std::cout << "[ ";
    for (const auto &elem : d)
    {
        std::cout << elem << " ";
    }
    std::cout << "]" << std::endl;
}

// [], at()
void test1(void)
{
    std::cout << "\nTEST1" << std::endl;

    std::deque<int> d{1, 2, 3, 4, 5};
    print(d);

    d = {2, 4, 5, 6};
    print(d);

    std::deque<int> d1(10, 0);    // ten 0s in the deque
    print(d1);

    // We use deques for operations at the front and the back, but they also
    // allow random access to the elements in constant time.
    d1[0] = 100;
    d1.at(1) = 200;
    print(d1);
}

// push_front(), pop_front(), push_back(), pop_back(), front(), back(), size()
void test2(void)
{
    std::cout << "\nTEST2" << std::endl;

    std::deque<int> d{0, 0, 0};
    print(d);

    d.push_back(5);
    d.push_back(8);
    print(d);

    d.push_front(2);
    d.push_front(1);
    print(d);

    std::cout << "Front: " << d.front() << std::endl;
    std::cout << "Back: " << d.back() << std::endl;
    std::cout << "Size: " << d.size() << std::endl;

    d.pop_back();
    d.pop_front();
    print(d);
}

// push_front(), push_back()
void test3(void)
{
    std::cout << "\nTEST3" << std::endl;

    // Insert all even numbers into the back of a deque and all odd numbers into
    // the front.
    std::vector<int> v{1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    std::deque<int> d;

    for (const auto &elem : v)
    {
        if (elem % 2 == 0)
        {
            d.push_back(elem);
        }
        else
        {
            d.push_front(elem);
        }
    }

    print(d);
}

// push_front(), push_back(), clear()
void test4(void)
{
    std::cout << "\nTEST4" << std::endl;

    // push_front() vs. push_back ordering.
    std::vector<int> v{1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    std::deque<int> d;

    for (const auto &elem : v)
    {
        d.push_front(elem);
    }
    print(d);
    d.clear();

    for (const auto &elem : v)
    {
        d.push_back(elem);
    }
    print(d);
}

// std::copy(), std::front_inserter(), std::back_inserter()
void test5(void)
{
    std::cout << "\nTEST5" << std::endl;

    // Same as test4(), but using std::copy.
    std::vector<int> v{1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    std::deque<int> d;

    
    // Same effect as the push_front().
    std::copy(v.begin(), v.end(), std::front_inserter(d));
    print(d);

    d.clear();

    // Same effect as the push_back().
    std::copy(v.begin(), v.end(), std::back_inserter(d));
    print(d);
}

int main(int argc, char *argv[])
{
    test1();
    test2();
    test3();
    test4();
    test5();
    return 0;
}
```

```plain

TEST1
[ 1 2 3 4 5 ]
[ 2 4 5 6 ]
[ 0 0 0 0 0 0 0 0 0 0 ]
[ 100 200 0 0 0 0 0 0 0 0 ]

TEST2
[ 0 0 0 ]
[ 0 0 0 5 8 ]
[ 1 2 0 0 0 5 8 ]
Front: 1
Back: 8
Size: 7
[ 2 0 0 0 5 ]

TEST3
[ 9 7 5 3 1 2 4 6 8 10 ]

TEST4
[ 10 9 8 7 6 5 4 3 2 1 ]
[ 1 2 3 4 5 6 7 8 9 10 ]

TEST5
[ 10 9 8 7 6 5 4 3 2 1 ]
[ 1 2 3 4 5 6 7 8 9 10 ]
```
