[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 1047. Remove All Adjacent Duplicates In String 1 (stack)

# LC - E - 1047. Remove All Adjacent Duplicates In String 1 (stack)



## Solutions in C++

### Solution 1

This solution uses STL `stack` container.

The original string does not get modified.

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the length of the string
* $S = O(n)$

**Solution:**

```cpp
class Solution {
public:
    string removeDuplicates(string s) {
        string ret;
        stack<char> st;

        // stack non-adjacent-duplicate characters only
        for (char ch : s)
        {
            if (!st.empty() && ch == st.top())
                st.pop();
            else
                st.push(ch);
        }

        // pop the characters out of the stack to build the string to return
        while (!st.empty())
        {
            ret += st.top();    // ret.push_back(st.top());
            st.pop();
        } // at this point, 'ret' contains the reverse of the string to return

        // reverse the string 'ret'
        reverse(ret.begin(), ret.end());

        return ret;
    }
};
```



### Solution 2

This solution uses STL `stack` container.

The original string gets modified.

Be careful when using the member function `erase()`. Following statement will erase everything from `s[i]` on:

```cpp
s.erase(i);
```

I had to spend quite some time debugging it.

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the length of the string
* $S = O(n)$

**Solution:**

```cpp
class Solution {
public:
    string removeDuplicates(string s) {
        int i = 0;
        stack<char> st;

        while (i < s.size())
        {          
            if (!st.empty() && s[i] == st.top())
            {
                st.pop();
                s.erase(i--, 1);
                s.erase(i, 1);
                continue;
            }

            st.push(s[i]);            
            ++i;
        }
        return s;
    }
};  
```



### Solution 3

This solution solves the problem without using the STL `stack` container. Instead, a string object is used to build up the string to return.

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the length of the string
* $S = O(n)$

**Solution:**

```cpp
class Solution {
public:
    string removeDuplicates(string s) {
        string ret;

        for (char ch : s)
        {
            if (!ret.empty() && ret.back() == ch)
                ret.pop_back();
            else
                ret += ch;
        }
        return ret;
    }
};
```
