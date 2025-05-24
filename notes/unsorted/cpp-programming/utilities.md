[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Utilities

# Utilities



## Common Utilities

The C++ standard library provides the same utilities as the C standard library. It also expands on them to provide higher-level abstractions, such as the `std::string` class, that hides some of the lower-level details. We have already been using a bunch of the STL classes for working with strings and performing IO.



## Strings

* Defined in the `<string>` header.
* A string is a sequence of characters that store the text.
* There are several ways to construct a string object.
* Once we have a string, we can also perform methods on that string.

### Example - Declaring Strings

```cpp
#include <iostream>
#include <string>

int main(int argc, char *argv[])
{
    // Declaring strings
    std::string name1 = "Kyungjae Lee"; // Anything wrapped with "" -> const char*.
    std::string name2("Hyeonseon Kim");
    std::string name3(name1);

    std::string name4 {'Y', 'e', 'n', 'a', ' ', 'L', 'e', 'e'};
    std::string last(name4, 5, 3); // Lee

    std::string line(20, '-'); // A string filled with 20 occurrences of '-'.
    std::string reverse(name1.rbegin(), name1.rend());

    std::cout << name1 << std::endl;
    std::cout << name2 << std::endl;
    std::cout << name3 << std::endl;
    std::cout << name4 << std::endl;
    std::cout << last << std::endl;
    std::cout << reverse << std::endl;
    std::cout << line << std::endl;

    return 0;
}
```

```cpp
Kyungjae Lee
Hyeonseon Kim
Kyungjae Lee
Yena Lee
Lee
eeL eajgnuyK
--------------------
```

### Example - Finding Strings

```cpp
#include <iostream>
#include <string>

using namespace std::string_literals;

int main(int argc, char *argv[])
{
    std::string name = "Kyungjae Lee";
    std::string lname {'L', 'e', 'e'};

    std::size_t pos = name.find(lname);
    if (pos != std::string::npos) // npos = No position found
    {
        std::cout << "Found the last name at: " << pos << std::endl;
    }

    std::cout << "Comma at: " << ("Hello, how are you?"s).find(",") << std::endl;

    return 0;
}
```

> L17: `"..."s` syntax requires the namespace `std::string_literals`.

```plain
Found the last name at: 9
Comma at: 5
```

### Example - Appending to a String

```cpp
#include <iostream>
#include <string>

int main(int argc, char *argv[])
{
    std::string name = "Kyungjae";
    std::string lname = "Lee";
    name.push_back(' ');

    for (char c : lname)
    {
        name.push_back(c);
    }

    std::cout << name << std::endl;
    return 0;
}
```

```plain
Kyungjae Lee
```

### Example - Inserting into a String

```cpp
#include <iostream>
#include <string>

int main(int argc, char *argv[])
{
    std::string name = "Kyungjae";
    
    name.insert(0, "My name is ");
    std::cout << name << std::endl;
    return 0;
}
```

```plain
My name is Kyungjae
```

### Example - Replace a Substring

```cpp
#include <iostream>
#include <string>

using namespace std::string_literals;

int main(int argc, char *argv[])
{
    std::string str = "I have heard that Kyungjae is a great engineer.";

    size_t pos = str.find("engineer");
    str.replace(pos, ("engineer"s).length(), "programmer");

    pos = str.find("heard");
    str.erase(pos, ("heard"s).length());
    str.insert(pos, "read somewhere");

    std::cout << str << std::endl;

    return 0;
}
```

```plain
I have read somewhere that Kyungjae is a great programmer.
```
