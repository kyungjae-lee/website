[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 344. Reverse String

# LC - E - 344. Reverse String



## Solutions in C++

### Solution 1

Even if `s` forms a string it is essentially a vector. The container vector does not provide the function `length()`. Make sure to use `size()`.

String class also provides the `size()` member function. (Maybe a good idea to use `size()` all throughout to minimize confusion.) 

**Complexity Analysis:**

* $T = O(n)$
* $S = O(1)$

**Solution:**

```cpp
class Solution {
public:
    void reverseString(vector<char>& s) {
        unsigned int len = s.size();

        for (int i = 0; i < len/2; i++) {
            swap(s[i], s[len-1-i]);
        }
    }
};
```



### Solution 2

This solution uses `beg` and `end` index variables.

**Complexity Analysis:**

* $T = O(n)$
* $S = O(1)$

**Solution:**

```cpp
class Solution {
public:
    void reverseString(vector<char>& s) {
        for (int beg = 0, end = s.size() - 1; beg < end; ++beg, --end) {
            swap(s[beg], s[end]);
        }
    }
};
```



### Solution 3

This solution uses C++ STL `stack` container to reverse a string. Note that this solution does not satisfy the condition "you must do this by modifying the input array in-place with O(1) extra memory".

**Complexity Analysis:**

* $T = O(n)$
* $S = O(n)$

**Solution:**

```cpp
class Solution {
public:
    void reverseString(vector<char>& s) {
        stack<char> st;

        // push the characters into the stack
        for (auto pos = s.cbegin(); pos != s.cend(); ++pos)
            st.push(*pos);

        // pop the characters from the stack and insert them back into the string
        for (auto pos = s.begin(); pos != s.end(); ++pos) {
            *pos = st.top();
            st.pop();
        }
    }
};
```



### Solution 4

This solution uses the C++ standard library function `reverse()` to reverse a string.
- `reverse()` function is defined as a template in the header file `<algorithm>`.
- It reverses the order of elements in the range [first, last) of any container.

**Complexity Analysis:**

* $T = O(n)$
* $S = O(1)$

**Solution:**

```cpp
class Solution {
public:
    void reverseString(vector<char>& s) {
        // reverse the order of elements in the range [beg, end)
        reverse(s.begin(), s.end());    
    }
};
```
