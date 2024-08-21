[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 258. Add Digits

# LC - E - 258. Add Digits



## Solutions in C++

### Solution 1

This solution uses iteration. 

**Complexity Analysis:**

*  $T = O(?)$ where $n$ is the number of digits in the given integer `num` (Analysis needed!)   
*  $S = O(1)$ as no additional memory is needed

**Solution:**

```cpp
class Solution {
public:
    int addDigits(int num) {
        int sum = 0;

        while (num > 9)
        {
            while (num > 0)
            {
                sum += num % 10;
                num /= 10;
            }

            num = sum;
            sum = 0;
        }

        return num;
    }
};
```

### Solution 2

This solution uses iteration. Notice how this solution improves the Solution 1 in the time complexity.

**Complexity Analysis:**

*  $T = O(n)$ where $n$ is the number of digits in the given integer `num`    
*  $S = O(1)$ as no additional memory is needed

**Solution:**

```cpp
class Solution {
public:
    int addDigits(int num) {
        int sum = 0;

        while (num > 0)
        {
            sum += num % 10;
            num /= 10;
            
            if (num == 0 && sum > 9)
            {
                num = sum;
                sum = 0;
            }
        }

        return sum;
    }
};
```

