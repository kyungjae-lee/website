[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 1209. Remove All Adjacent Duplicates In String 2 (stack, recursion)

# LC - E - 1209. Remove All Adjacent Duplicates In String 2 (stack, recursion)



## Solutions in C++

### Solution 1

This solution uses STL `stack` container.

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the length of the string
* $S = O(n)$ since this solution uses additional memory for stack

**Solution:**

```cpp
class Solution {
public:
    string removeDuplicates(string s, int k) {
        stack<pair<char, int> > st; // element type is a pair of character and its count
        string ret = "";

        // stack the characters, leaving non-k-consecutive ones only
        for (char ch : s)
        {
            if (st.empty() || ch != st.top().first)
                st.push({ch, 1});
            else
            {
                st.top().second++;          // increment the count

                if (st.top().second == k)   // check the count
                    st.pop();    
            }
        }

        // pop the characters out of the stack to build the string to return
        while (!st.empty())
        {
            for (int i = 0; i < st.top().second; i++) // while (st.top().second--)
                ret += st.top().first;

            st.pop();
        } // at this point, 'ret' contains the reverse of the string to return

        // reverse the string 'ret'
        reverse(ret.begin(), ret.end());

        return ret;
    }
};
```



### Solution 2

This solution uses recursion. (My first solution)

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the length of the string
* $S = O(n)$ where $n$ is the length of the string

**Solution:**

```cpp
class Solution {
public:
    string removeDuplicates(string s, int k) {
        string ret;
        int count = 1;
        bool isComplete = true;

        for (char ch : s)
        {
            if (ret.empty())
            {
                ret.push_back(ch);
            }
            else
            {
                if (ch == ret.back())
                    ++count;
                else
                    count = 1;

                ret.push_back(ch);

                if (count == k)
                {
                    isComplete = false;

                    while (count > 0)
                    {
                        ret.pop_back();
                        count--;
                    }

                    count = 1;
                }
            }
        }

        if (isComplete) 
            return ret;

        return removeDuplicates(ret, k);
    }
};
```

