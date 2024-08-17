[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > String Manipulation

# String Manipulation



## String Tokenization

To use the C++ feature, we have to convert a string to a string stream. Then using `getline()` function we can do the task. The `getline()` function takes the string stream, another string to send the output, and the  delimiter to stop the stream from scanning.

For example,

```cpp
#include <iostream>
#include <vector>
#include <sstream>

using namespace std;

int main(int argc, char *argv[])
{
    string str = "Hello, world! My name is Kyungjae Lee.";
    stringstream ss(str);	// convert str into string stream
    
    vector<string> tokens;
    string tok;
    
    // tokenize using space as a delimiter
    while (getline(ss, tok, ' '))
        tokens.push_back(temp_str);
    
    // print tokens
    for (auto tok : tokens)
        cout << tok << endl;
    
    return 0;
}
```

```plain
Hello,
world!
My
name
is
Kyungjae
Lee.
```

