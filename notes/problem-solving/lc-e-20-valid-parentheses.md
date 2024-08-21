[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 20. Valid Parentheses (stack, map)

# LC - E - 20. Valid Parentheses (stack, map)



## Solutions in C++

### Solution 1

This solution uses STL `stack`, and `map` container.

**Complexity Analysis:**

*  $T = O(n)$ where $n$ is the length of the given string `s`
*  $S = O(n)$ as additional memory space is used for `stack` and `map` container

**Solution:**

```cpp
class Solution {
public:
    bool isValid(string s) {
        stack<char> st;
        map<char, char> m = { {'(', ')'}, {'{', '}'}, {'[', ']'} };        
    
        st.push(s[0]); // since s contains at least one character by the constraints

        for (int i = 1; i < s.size(); i++)
        {
            if (!st.empty() && m[st.top()] == s[i])
                st.pop();
            else
                st.push(s[i]);
        }       
        
        return st.empty();
    }
};
```



### Solution 2

This solution uses STL `stack`, and `map` container. 

**Complexity Analysis:**

*  $T = O(n)$ where $n$ is the length of the given string `s`
*  $S = O(n)$ as additional memory space is used for `stack` and `map` container

**Solution:**

```cpp
class Solution {
public:
    bool isValid(string s) {
        stack<char> st;
        map<char, char> m = { {'(', ')'}, {'{', '}'}, {'[', ']'} };     

        for (auto ch : s)
        {
            // if open parenthesis
            if (ch == '(' || ch == '{' || ch == '[')
                st.push(ch);
            // if closing parenthesis 
            else if (!st.empty() && m[st.top()] == ch)
                st.pop();
            else
                return false;
        }
        
        return st.empty();	// cannot be 'return true' as it cannot catch "[" like case
    }
};
```

