[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 338. Counting Bits

# LC - E - 338. Counting Bits



## Solutions in C++

### Solution 1

**Complexity Analysis:**

*  $T = O(n\log n)$ where $n$ is the given integer 
*  $S = O(1)$ as no additional memory is needed

**Solution:**

```cpp
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> ans;

        /* outerloop T = O(n), inner loop T = O(log n) */
        for (int i = 0; i <= n; i++)
        {
            int sum = 0;
       		int num = i;
            
            while (num > 0 )
            {
                sum += num % 2;
                num /= 2;
            }

            ans.push_back(sum);
            sum = 0;
        }

        return ans;
    }
};
```



### Solution 2

This solution focuses on the relationship between the $i^{th}$ element and the $(i/2)^{th}$ element, and the relationship between odd and even elements. It has better time complexity than that of Solution 1's.

**Complexity Analysis:**

*  $T = O(n)$ where $n$ is the given integer     
*  $S = O(1)$ as no additional memory is needed

**Solution:**

```cpp
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> ans(n + 1);

        ans.at(0) = 0;	/* this operation will result it out-of-range error on a vector of size 0 */

        for (int i = 1; i <= n; i++)
            ans.at(i) = ans.at(i / 2) + i % 2;

        return ans;
    }
};
```

```c
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> ans(n + 1);

        ans[0] = 0;	/* this operation will result in undefined behavior on a vector of size 0 */

        for (int i = 1; i <= n; i++)
            ans[i] = ans[i / 2] + i % 2;

        return ans;
    }
};
```

Calling operator `[]`, `front()`, and `back()` for an empty container always results in undefined behavior. Only `at()` operator checks for range validity.
