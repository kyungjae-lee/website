[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 1. Two Sum

# LC - E - 1. Two Sum




## Solutions in C++

### Solution 1

Brute-force approach - Checking all possible combinations with two nested loops.

**Complexity Analysis:**

* T = O(n^2^)

* S = O(1)

**Solution:**

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        for (int i = 0; i < n - 1; ++i)
        {
            for (int j = i + 1; j < n; ++j)
            {
                if (nums[i] + nums[j] == target)
                {
                    return {i, j};
                }   
            }
        }

        // No solution found
        return {};
    }
};
```



### Solution 2

Instead of checking all possible combinations with two nested loops (**O(n²)**), this solution uses a **hash table (`unordered_map`)** to reduce the time complexity to **O(n)**.

**Complexity Analysis:**

* T = O(n)
* S = O(1)

**Solution:** Two-pass

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> um;
        int n = nums.size();

        // Build the hash table to map values to indices
        for (int i = 0; i < n; ++i)
        {
            um[nums[i]] = i;
        }

        // Find the complement
        for (int i = 0; i < n; ++i)
        {
            int complement = target - nums[i];
            
            if (um.count(complement) && um[complement] != i)
            {
                return {i, um[complement]};
            }
        }

        // No solution found
        return{};
    }
};
```

**Solution:** One-pass

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> um;
        int n = nums.size();

        for (int i = 0; i < n; ++i)
        {
            int complement = target - nums[i];

            if (um.count(complement))
            {
                return {um[complement], i};
            }

            um[nums[i]] = i;
        }

        // No solution found
        return{};
    }
};
```

