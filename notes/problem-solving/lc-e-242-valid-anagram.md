[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 242. Valid Anagram (undordered_map)

# LC - E - 242. Valid Anagram (undordered_map)



## Solutions in C++

### Solution 1

This solution uses STL `unordered_map` container.

**Complexity Analysis:**

*  $T = O(n)$ where $$n$$ is the number of elements in the given string `s` and `t`    

* $S = O(n)$ since the `unordered_map` of size $$n$$ is used

**Solution:**

```cpp
class Solution {
public:
	bool isAnagram(string s, string t) {
		unordered_map<char, int> m;

        // add characters of the string s to the map m
        for (auto ch : s)
            m[ch]++;

        // remove characters of the string t from the map m
        for (auto ch : t)
        {
            if (--m[ch] == 0)
                m.erase(ch);
        }

        // if t is an anagram of s, m will be empty at this point
        return m.empty();
    }
};
```

