[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 263. Ugly Number

# LC - E - 263. Ugly Number



## Solutions in C++

### Solution 1

This solution uses recursion. 

**Complexity Analysis:**

*  $T = O(\log n)$ where $n$ is the given number ($O(\log n)$ because we are dividing the number by 2, 3 and 5 in while loop repeatedly)
*  $S = O(1)$ as no additional memory is needed

**Solution:**

```cpp
class Solution {
public:
    bool isUgly(int n) {
        if (n <= 0) return false;

        while (n % 2 == 0) n /= 2;
        while (n % 3 == 0) n /= 3;
        while (n % 5 == 0) n /= 5;

        return n == 1;
    }
};
```

### Solution 2

This solution uses recursion. 

**Complexity Analysis:**

*  $T = O(?)$ (Analysis needed!)
*  $S = O(?)$ (Analysis needed!)

**Solution:**

```cpp
class Solution {
public:
    bool isUgly(int n) {
        if (n <= 0) return false;
        if (n < 7) return true;
        if (n % 2 == 0) return isUgly(n/2);
        if (n % 3 == 0) return isUgly(n/3);
        if (n % 5 == 0) return isUgly(n/5);
        return false;
    }
};
```

