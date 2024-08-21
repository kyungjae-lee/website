[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - M - 658. Find K Closest Elements (multimap, vector, sort)

# LC - M - 658. Find K Closest Elements (multimap, vector, sort)



## Solutions in C++

### Solution 1

**Complexity Analysis:**

* $T = O(n\log n)$, where $n$ is the number of elements in `arr`
* $S = O(n)$, additional memory space used for `multimap`

**Solution:**

```cpp
// #include <map>

class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {

        multimap<int, int> m;
        vector<int> ret;

        // Loop through the 'arr', insert each pair {|arr[i] - x|, arr[i]} into multimap
        // T = O(nlogn), where n is the size of 'arr'
        for (int i = 0; i < arr.size(); i++)
        {
            int diff = abs(arr[i] - x);
            m.insert({diff, arr[i]});   // T = O(logn)
        }

        // T = O(k), where k <= n
        for (auto it = m.begin(); k-- > 0; it++)
            ret.push_back(it->second);

        // T = O(nlogn)
        sort(ret.begin(), ret.end());

        return ret;
    }
};
```
