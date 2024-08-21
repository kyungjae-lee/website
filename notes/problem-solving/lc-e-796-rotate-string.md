[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 796. Rotate String (sprintf, strstr)

# LC - E - 796. Rotate String (sprintf, strstr)



## Solutions in C

### Solution 1

This solution uses C standard library string manipulation functions such as `sprintf`, `strstr` and `strlen`.

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the length of the given string `s`
* $S = O(1)$ 

**Solution:**

```cpp
#include <stdio.h>	// sprintf
#include <string.h>	// strstr, strlen

bool rotateString(char * s, char * goal){
    if (strlen(s) != strlen(goal))
        return false;

    // dynamically allocate memory for 'str' and initialize all bytes with 0 ('\0')
    char *str = (char *)calloc(strlen(s) * 2 + 1, sizeof(char));
    sprintf(str, "%s%s", s, s);
    
    // thanks to 'calloc' the very last elelement of 'str' is already '\0'

    if (strstr(str, goal) == NULL)
        return false;

    return true;
}
```

