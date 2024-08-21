[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 1122. Relative Sort Array (map, multiset)

# LC - E - 1122. Relative Sort Array (map, multiset)



## Solutions in C++

### Solution 1

This solution uses STL `map` container.

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the length of the given vector `arr1`
* $S = O(n)$ as additional memory is used for `map`

**Solution:**

```cpp
class Solution {
public:
    vector<int> relativeSortArray(vector<int>& arr1, vector<int>& arr2) {
        map<int, int> m;

        for (auto n : arr1)
            m[n]++;

        arr1.clear();

        for (auto n : arr2)
        {
            while (m[n]-- > 0)
                arr1.push_back(n);
            
            m.erase(n);
        }

        for (auto p : m)
        {
            while (p.second-- > 0)
                arr1.push_back(p.first);
        }

        return arr1;
    }
};
```



### Solution 2

This solution uses STL `multiset` container.

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the length of the given vector `arr1`
* $S = O(n)$ as additional memory is used for `multiset`

**Solution:**

```cpp
class Solution {
public:
    vector<int> relativeSortArray(vector<int>& arr1, vector<int>& arr2) {
        multiset<int> ms;

        for (auto n : arr1)
            ms.insert(n);

        arr1.clear();

        for (auto n : arr2)
        {
            auto pos = ms.find(n);
            while (pos != ms.end())
            {
                arr1.push_back(n);
                ms.erase(pos);
                pos = ms.find(n);
            }
        }

        for (auto n : ms)
            arr1.push_back(n);

        return arr1;
    }
};
```

