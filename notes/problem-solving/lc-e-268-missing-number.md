[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 268. Missing Number (bit manipulation)

# LC - E - 268. Missing Number (bit manipulation)



## Solutions in C++

### Solution 1

This solution uses bit manipulation. `XOR` operations has the following properties:

* `XOR`ing a number by itself results in 0.
* `XOR` is commutative and associative.

**Complexity Analysis:**

*  $T = O(n)$ where $n$ is the number of elements of the given vector    
*  $S = O(1)$

**Solution:**

```cpp
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int ret = nums.size()
        int i = 0;

        for (auto n : nums)
        {
            ret ^= n;
            ret ^= i;
            ++i;
        }

        return ret;
    }
};
```



### Solution 2

This solution uses the summation formula $sum_{i = 0}^{n}(n) = n(n+1)/2$.

**Complexity Analysis:**

*  $T = O(n)$ where $n$ is the number of elements of the given vector    
*  $S = O(1)$

**Solution:**

```cpp
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int n = nums.size();
        int ret = n * (n + 1) / 2;

        for (auto n : nums)
            ret -= n;       

        return ret;
    }
};
```



### Solution 3

This solution uses the `std::sort` algorithm.

**Complexity Analysis:**

*  $T = O(n\log n)$ where $n$ is the number of elements of the given vector    
*  $S = O(1)$

**Solution:**

```cpp
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        std::sort(nums.begin(), nums.end());
        int n = nums.size();

        for (int i = 0; i < nums.size(); ++i)
        {
            if (nums[i] != i)
                return i;
        }

        return n;
    }
};
```



## Solutions in C

### Solution 1

This solution uses bit manipulation. `XOR` operations has the following properties:

* `XOR`ing a number by itself results in 0.
* `XOR` is commutative and associative.

**Complexity Analysis:**

*  $T = O(n)$ where $n$ is the number of elements of the given array    
*  $S = O(1)$

**Solution:**

```c
int missingNumber(int* nums, int numsSize){
    int ret = numsSize;
    int i;

    for (i = 0; i < numsSize; ++i)
    {
        ret ^= nums[i];
        ret ^= i;
    }

    return ret;
}
```

