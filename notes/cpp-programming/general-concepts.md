[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > General Concepts

# General Concepts



## Header Files

The Standard Template Library (STL) is organized into multiple header files, and each new C++ standard typically introduces more.



## Standard Namespace

All STL functions and classes are defined within the `std` namespace. 

e.g., `std::string`, `std::queue`, etc.



## Exception Handling

C++ supports explicit exception handling and enables the creation of separate execution branches based on the type of exception thrown. It provides three keywords for handling exceptions:

* try
* catch
* throw

Additionally, C++11 introduced the `noexcept` specifier, which can be applied to functions to indicate that they are not expected to throw exceptions.

### Example

```cpp
#include <iostream>
#include <exception>

// A custom exception class extending the standard exception
class extended_exception : public std::exception
{
public:
    extended_exception(const std::string &w) : w(w) {}
    const char* what() const noexcept
    {
        return w.c_str();
    }
private:
    std::string w;
};

class myclass
{
public:
    myclass()
    {
        throw extended_exception("Unable to do what I need");
        //throw 10;
        //throw "error";
    }
};

int main(int argc, char *argv[])
{
    // Try block: Run this code block and if something goes wrong handle it
    // appropriately.
    try
    {
        myclass mc;
    }
    catch (std::exception &except)
    {
        // Via polymorphism, the following code will use the
        // extended_exception's 'what()' function.
        std::cout << except.what() << std::endl;
        return 1;
    }
    catch (int i)
    {
        std::cout << "I caught an int value" << std::endl;
        return 2;
    }
    catch (...)
    {
        std::cout << "Catchall" << std::endl;
    }

    return 0;
}
```



## Memory Management

In earlier versions of C++, it was common practice to use `new` and `delete` to manually manage dynamic memory on the heap. In the 1990s, the standard introduced `auto_ptr` as an attempt to automate memory management. However, `auto_ptr` had several design flaws—particularly around ownership semantics—and was eventually deprecated in C++11. It was replaced by `unique_ptr` and `shared_ptr`, which provide safer and more predictable behavior.

In modern C++, there is rarely a good reason to use raw owning pointers. Smart pointers are the preferred approach for managing dynamic memory.

### Example

```cpp
#include <iostream>
#include <memory> // unique_ptr, make_unique

class widget
{
public:
    widget(int n) : n (n)
    {
        std::cout << "Constructing widget " << n << std::endl;
    }
    virtual ~widget()
    {
        std::cout << "Destroying widget " << n << std::endl;
    }

private:
    int n;
};

int main(int argc, char *argv[])
{
    widget *w = new widget(1); // This requires deleting w manually.
    //delete w;

    // Smart pointer handles memory automatically.
    // Here's what's happening:
    // 1. Pass to the make_unique function, which is a templated function.
    // 2. Constructor calls 'new' inside make_unique and then pass that
    //    unique_ptr reference over to the 'up' variable.
    // Now, the 'up' is the sole owner of that resource. So when it goes out of
    // scope, because now it exists on the stack not in the heap, it
    // automatically calls the destructor on the widget object.
    std::unique_ptr<widget> up = std::make_unique<widget>(2);

    return 0;
}
```

```plain
Constructing widget 1
Constructing widget 2
Destroying widget 2
```

> L23: You can use Valgrind to check for memory leaks.
>
> L33: With `unique_ptr`, I didn’t have to delete the object manually—it was automatically deallocated when the pointer went out of scope.
