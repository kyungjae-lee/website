[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Introduction to STL Algorithms

# Introduction to STL Algorithms



## Algorithms

* STL algorithms operate on sequences of container elements provided to them by an interator.
* STL has many common and useful algorithms.
* Many algorithms require extra information in order to do their work:
  * Functors (function objects)
  * Function pointers
  * Lambda expressions (C++11) - The way to go in modern C++!
* For more information, visit [http://en.cppreference.com/w/cpp/algorithm](http://en.cppreference.com/w/cpp/algorithm).



## Algorithms and Iterators

* STL algorithms are declared in the `<algorithm>` header file.
* Different containers support different types of iterators, which determine the algorithms that can be used with them.
* All STL algorithms expect iterators as arguments which determine the sequence obtained from the container.

### Iterator Invalidation

* Iterators point to elements within a container, but they can become invalid during processing. For example, suppose we are iterating over a vector of 10 elements and call `clear()` on the vector during iteration. The iterator is not automatically updated and may continue to reference an invalid location, leading to undefined behavior.
* Every STL container has documentation about when iterators become invalid. It's something to be aware of as you move on to more advanced STL work.

### Example - `find()` with Primitive Types

* The `find()` algorithm tries to locate the first occurrence of an element in a container.

* Lots of variations.

* Returns an interator pointing to the located element or `end()`.

* Example:

  ```cpp
  #include <iostream>
  #include <algorithm>
  #include <vector>
  
  int main(int argc, char *argv[])
  {
      std::vector<int> v{1, 2, 3};
      auto pos = std::find(v.begin(), v.end(), 3);
      if (pos != v.end())
      {
          std::cout << *pos << std::endl;	// 3
  	}
  }
  ```

  The exact same pattern can be used to find the first occurrence in a list or another type of container. We don't need to know the details about how the container is implemented or how complicated finding an element might be behind the scenes. This is the power of the STL algorithms.

### Example - `find()` with User-Defined Types

* `find()` needs to be able to compare object.

* `operator==` is used and must be provided by your class.

* Example:

  ```cpp
  #include <iostream>
  #include <algorithm>
  #include <vector>
  
  int main(int argc, char *argv[])
  {
      std::vector<player> team { /* assume initialized */ };
      player p{"Kyungjae", 100, 12};
      
      auto pos = std::find(team.begin(), team.end(), p);
      
      if (pos != team.end())
      {
          std::cout << *pos << std::endl;	// operator<< called
      }
  
      return 0;
  }
  ```

  > In this case the `operator==` must be provided by the class `player`.

### Example - `for_each()`

* `for_each()` algorithm applies a function to each element in the iterator sequence.

* Function must be provided to the algorithm as:

  * Functor (function object)
  * Function pointer
  * Lambda expression (C++11) - The way to go in modern C++!

* Example - Using a functor:

  ```cpp
  #include <iostream>
  #include <vector>
  #include <algorithm>
  
  struct square_functor
  {
      // () operator (i.e., function call operator) overloading
      void operator()(int x)
      {
          std::cout << x * x << " ";
      }
  };
  
  int main(int argc, char *argv[])
  {
      square_functor s;	// Function object
      std::vector<int> v{1, 2, 3, 4};
      std::for_each(v.begin(), v.end(), s);	// Functor is passed
      return 0;
  }
  ```

  ```plain
  1 4 9 16
  ```

* Example - Using a function pointer:

  ```cpp
  #include <iostream>
  #include <vector>
  #include <algorithm>
  
  void square(int x)
  {
      std::cout << x * x << " ";
  }
  
  int main(int argc, char *argv[])
  {
      std::vector<int> v{1, 2, 3, 4};
      std::for_each(v.begin(), v.end(), square);	// Function pointer is passed
      return 0;
  }
  ```

  ```plain
  1 4 9 16
  ```

* Example - Using a lambda expression:

  ```cpp
  #include <iostream>
  #include <vector>
  #include <algorithm>
  
  int main(int argc, char *argv[])
  {
      std::vector<int> v{1, 2, 3, 4};
      std::for_each(v.begin(), v.end(), [](int x) {std::cout << x * x << " "; });
      return 0;
  }
  ```

  ```plain
  1 4 9 16
  ```

  One of the benefits of using lambda expressions is that they can be defined directly at the point of use.



## Project: Various Usages of Algorithms

The following project demonstrates the use of only a small subset of STL algorithms. Note how consistent the usage pattern is across different containers.

```cpp
#include <iostream>
#include <vector>
#include <list>
#include <algorithm>
#include <cctype>	// toupper(), tolower()

class person
{
public:
    person() = default;
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
    std::string name;
    int age;
};

// find()
void test1(void)
{
    std::cout << "\nTEST1" << std::endl;

    std::vector<int> v{1, 2, 3, 4, 5};
    auto pos = std::find(std::begin(v), std::end(v), 1); // v.begin(), v.end()
    
    if (pos != std::end(v))
    {
        std::cout << "Found the number: " << *pos << std::endl;
    }
    else
    {
        std::cout << "Couldn't find the number." << std::endl;
    }

    std::list<person> l{
        {"Kyungjae", 30},
        {"Sunny", 20},
        {"Yena", 10}
    };
    auto pos1 = std::find(l.begin(), l.end(), person{"Yena", 10});

    if (pos1 != l.end())
    {
        std::cout << "Found the person. " << std::endl;
    }
    else
    {
        std::cout << "Counldn't find the person." << std::endl;
    }
}

// count() 
void test2(void)
{
    std::cout << "\nTEST2" << std::endl;
    
    std::vector<int> v{1, 2, 3, 4, 5, 1, 2, 1};

    int n = std::count(v.begin(), v.end(), 1);
    std::cout << n << " occurrences found." << std::endl;
}

// count_if()
// Note: count_if() algorithm counts the number of occurrences of an element in
// a container based on a predcate using a lambda expression.
void test3(void)
{
    std::cout << "\nTEST3" << std::endl;

    std::vector<int> v{1, 2, 3, 4, 5, 1, 2, 1, 100};

    // Count only if the element is even.
    int n = std::count_if(v.begin(), v.end(), [](int x) { return x % 2 == 0; });
    std::cout << n << " even numbers found." << std::endl;

    // Count only if the element is odd.
    n = std::count_if(v.begin(), v.end(), [](int x) { return x % 2 != 0; });
    std::cout << n << " odd numbers found." << std::endl;

    // Count only if the element >= 5.
    n = std::count_if(v.begin(), v.end(), [](int x) { return x >= 5; });
    std::cout << n << " numbers are >= 5." << std::endl;
}

// replace()
void test4(void)
{
    std::cout << "\nTEST4" << std::endl;

    std::vector<int> v{1, 2, 3, 4, 5, 1, 2, 1};
    for (const auto n : v)
    {
        std::cout << n << " ";
    }
    std::cout << std::endl;

    std::replace(v.begin(), v.end(), 1, 100); // Replace all occurrences of 1 with 100.
    for (const auto n : v)
    {
        std::cout << n << " ";
    }
    std::cout << std::endl;
}

// all_of()
void test5()
{
    std::cout << "\nTEST5" << std::endl;

    std::vector<int> v{1, 3, 5, 7, 9, 1, 3, 13, 19, 5};

    if (std::all_of(v.begin(), v.end(), [](int x) { return x > 10; }))
    {
        std::cout << "All the elements are > 10." << std::endl;
    }
    else
    {
        std::cout << "Not all the elements are > 10." << std::endl;
    }

    if (std::all_of(v.begin(), v.end(), [](int x) { return x < 20; }))
    {
        std::cout << "All the elements are < 20." << std::endl;
    }
    else
    {
        std::cout << "Not all the elements are < 20." << std::endl;
    }
}

// transform()
void test6(void)
{
    std::cout << "\nTEST6" << std::endl;

    std::string s{"This is a test string."};
    std::cout << "Before transform: " << s << std::endl;
    std::transform(s.begin(), s.end(), s.begin(), ::toupper);
    std::cout << "After transform: " << s << std::endl;
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

> L148: If no namespace is specified before the scope resolution operator `::`, as in `::toupper`, it refers to the global scope.

```plain

TEST1
Found the number: 1
Found the person.

TEST2
3 occurrences found.

TEST3
4 even numbers found.
5 odd numbers found.
2 numbers are >= 5.

TEST4
1 2 3 4 5 1 2 1
100 2 3 4 5 100 2 100

TEST5
Not all the elements are > 10.
All the elements are < 20.

TEST6
Before transform: This is a test string.
After transform: THIS IS A TEST STRING.
```

