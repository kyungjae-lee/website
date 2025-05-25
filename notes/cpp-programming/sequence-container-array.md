[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Sequence Container - `std::array` (C++11)

# Sequence Container - `std::array` (C++11)



## `std::array` (C++11)

* Declared in the `<array>` header file.
* Fixed size array of any type - the size must be known at compile time.
* Direct element access - Element access happens in constant time. (i.e., O(1))
* Provides access to the underlying raw array.
  * `std::array` is implemented behind the scenes by the STL as a wrapper class around a raw array. 
  * We can get access to that array address if we need to and we can use it with libraries that use raw pointers.
* `std::array` differs from the raw array in that:
  * It's an object type.
  * It alwyas has its size associated with it.
  * It doesn't decay to a pointer to the first element of the array when passed into a function.
* Use `std::array` for a fixed-size array instead of raw arrays unless you have a compelling reason to do so.
* All iterators are available and do not become invalid since `std::array` is a fixed-size structure.

### Initialization and Assignment

```cpp
// Initialization
std::array<int, 5> arr1{ {1, 2, 3, 4, 5} };

// Initialization
std::array<std::string, 3> arr2{
    std::string{"Kyungjae"},
    "Sunny",	// C-style string will be converted to a std::string.
    std::string{"Yena"}
};

// Assignment
arr1 = {2, 4, 6, 8, 10};
```

### Common Methods

```cpp
std::array<int, 5> arr1{1, 2, 3, 4, 5};
std::array<int, 5> arr2{10, 20, 30, 40, 50};

std::cout << arr1.size();		// 5
std::cout << arr1.at(0);		// 1 (Supports out-of-bounds check)
std::cout << arr1[1];			// 2 (Does not support out-of-bounds check)
std::cout << arr1.front();		// 1 (Returns reference to the first element)
std::cout << arr1.back();		// 5 (Returns reference to the last element)
std::cout << arr1.empty();		// 0 (false)
std::cout << arr1.max_size();	// 5
arr1.swap(arr2);				// Swaps the 2 arrays
int *p_data = arr1.data();		// Returns the raw array address
```



## Project: Usage of `std::array`

```cpp
#include <iostream>
#include <array>
#include <algorithm>
#include <numeric>  // accumulate()

// Display any array of integers of size 5 using range-based for loop
void print(const std::array<int, 5> &arr)
{
    std::cout << "[ ";
    for (auto const &n : arr)
    {
        std::cout << n << " ";
    }
    std::cout << "]" << std::endl;
}

// front(), back()
void test1(void)
{
    std::cout << "\nTEST1" << std::endl;

    std::array<int, 5> arr1{1, 2, 3, 4, 5}; // Double {{ }} if C++11.
    std::array<int, 5> arr2;

    print(arr1);
    print(arr2);    // Elements are not initialized (contains garbage)

    arr2 = {10, 20, 30, 40, 50};

    print(arr1);
    print(arr2);

    std::cout << "Size of arr1 is: " << arr1.size() << std::endl;   // 5
    std::cout << "Size of arr2 is: " << arr2.size() << std::endl;   // 5

    arr1[0] = 1000;     // Does not support out-of-bounds check.
    arr1.at(1) = 2000;  // Supports out-of-bounds check.
    print(arr1);
    
    std::cout << "Front of arr2: " << arr2.front() << std::endl;    // 10
    std::cout << "Back of arr2: " << arr2.back() << std::endl;      // 50
}

// fill()
void test2(void)
{
    std::cout << "\nTEST2" << std::endl;

    std::array<int, 5> arr1{1, 2, 3, 4, 5}; // Double {{ }} if C++11.
    std::array<int, 5> arr2{10, 20, 30, 40, 50};

    print(arr1);
    print(arr2);

    arr1.fill(0);

    print(arr1);
    print(arr2);

    arr1.swap(arr2);

    print(arr1);
    print(arr2);
}

// data()
void test3(void)
{
    std::cout << "\nTEST3" << std::endl;

    std::array<int, 5> arr{1, 2, 3, 4, 5}; // Double {{ }} if C++11.

    int *ptr = arr.data(); // Address of the raw array within the object.
    std::cout << ptr << std::endl;
    *ptr = 10000;

    print(arr);
}

// sort()
void test4(void)
{
    std::cout << "\nTEST4" << std::endl;

    std::array<int, 5> arr{2, 1, 4, 5, 3}; // Double {{ }} if C++11.
    print(arr);

    std::sort(arr.begin(), arr.end());
    print(arr);
}

// min_element(), max_element()
void test5(void)
{
    std::cout << "\nTEST5" << std::endl;

    std::array<int, 5> arr{2, 1, 4, 5, 3}; // Double {{ }} if C++11.

    // Note: min_element() and max_element() returns 'iterators' to the elements
    // not the elements. 
    std::array<int, 5>::iterator min = std::min_element(arr.begin(), arr.end());
    auto max = std::max_element(arr.begin(), arr.end());
    std::cout << "min: " << *min << ", max: " << *max << std::endl;
}

// adjacent_find()
void test6(void)
{
    std::cout << "\nTEST6" << std::endl; 

    std::array<int, 5> arr{2, 1, 3, 3, 5};  // Double {{ }} if C++11.

    auto adj = std::adjacent_find(arr.begin(), arr.end());
    if (adj != arr.end())
    {
        std::cout << "Adjacent element found with value: " << *adj << std::endl;
    }
    else
    {
        std::cout << "No adjacent elements found." << std::endl;
    }
}

// accumulate()
void test7(void)
{
    std::cout << "\nTEST7" << std::endl; 

    std::array<int, 5> arr{1, 2, 3, 4, 5};  // Double {{ }} if C++11.

    // Note: Ensure that the data type of the first template parameter of the
    // array, 'sum', and the third argument to the accumulate() function are
    // all the same.
    int sum = std::accumulate(arr.begin(), arr.end(), 0);
    std::cout << "Sum of the elements in arr is: " << sum << std::endl;
}

// count()
void test8(void)
{
    std::cout << "\nTEST8" << std::endl; 

    std::array<int, 10> arr{1, 2, 3, 1, 2, 3, 3, 3, 3, 3};
    // Double {{ }} if C++11.

    int cnt = std::count(arr.begin(), arr.end(), 3);
    std::cout << "Found 3: " << cnt << " times." << std::endl;
}

// count_if()
void test9(void)
{
    std::cout << "\nTEST9" << std::endl; 

    std::array<int, 10> arr{1, 2, 3, 50, 60, 70, 80, 200, 300, 400};
    // Double {{ }} if C++11.

    // Find how many numbers are between 10 and 200 -> 50, 60, 70, 80.
    int cnt = std::count_if(arr.begin(), arr.end(), 
                            [](int x) { return x > 10 && x < 200; });
    std::cout << "Found: " << cnt << " matches." << std::endl;
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
    return 0;
}
```

```plain

TEST1
[ 1 2 3 4 5 ]
[ 0 0 1823312064 32761 8 ]
[ 1 2 3 4 5 ]
[ 10 20 30 40 50 ]
Size of arr1 is: 5
Size of arr2 is: 5
[ 1000 2000 3 4 5 ]
Front of arr2: 10
Back of arr2: 50

TEST2
[ 1 2 3 4 5 ]
[ 10 20 30 40 50 ]
[ 0 0 0 0 0 ]
[ 10 20 30 40 50 ]
[ 10 20 30 40 50 ]
[ 0 0 0 0 0 ]

TEST3
0x1fb35ffdf0
[ 10000 2 3 4 5 ]

TEST4
[ 2 1 4 5 3 ]
[ 1 2 3 4 5 ]

TEST5
min: 1, max: 5

TEST6
Adjacent element found with value: 3

TEST7
Sum of the elements in arr is: 15
PS D:\workspace\sel> g++ .\array.cpp
PS D:\workspace\sel> .\a.exe        

TEST1
[ 1 2 3 4 5 ]
[ 0 0 1823312064 32761 8 ]
[ 1 2 3 4 5 ]
[ 10 20 30 40 50 ]
Size of arr1 is: 5
Size of arr2 is: 5
[ 1000 2000 3 4 5 ]
Front of arr2: 10
Back of arr2: 50

TEST2
[ 1 2 3 4 5 ]
[ 10 20 30 40 50 ]
[ 0 0 0 0 0 ]
[ 10 20 30 40 50 ]
[ 10 20 30 40 50 ]
[ 0 0 0 0 0 ]

TEST3
0xd0b8dffc80
[ 10000 2 3 4 5 ]

TEST4
[ 2 1 4 5 3 ]
[ 1 2 3 4 5 ]

TEST5
min: 1, max: 5

TEST6
Adjacent element found with value: 3

TEST7
Sum of the elements in arr is: 15

TEST8
Found 3: 6 times.
PS D:\workspace\sel> g++ .\array.cpp
PS D:\workspace\sel> .\a.exe        

TEST1
[ 1 2 3 4 5 ]
[ 0 0 1823312064 32761 8 ]
[ 1 2 3 4 5 ]
[ 10 20 30 40 50 ]
Size of arr1 is: 5
Size of arr2 is: 5
[ 1000 2000 3 4 5 ]
Front of arr2: 10
Back of arr2: 50

TEST2
[ 1 2 3 4 5 ]
[ 10 20 30 40 50 ]
[ 0 0 0 0 0 ]
[ 10 20 30 40 50 ]
[ 10 20 30 40 50 ]
[ 0 0 0 0 0 ]

TEST3
0x8add5ffc10
[ 10000 2 3 4 5 ]

TEST4
[ 2 1 4 5 3 ]
[ 1 2 3 4 5 ]

TEST5
min: 1, max: 5

TEST6
Adjacent element found with value: 3

TEST7
Sum of the elements in arr is: 15

TEST8
Found 3: 6 times.

TEST9
Found: 4 matches.
```

