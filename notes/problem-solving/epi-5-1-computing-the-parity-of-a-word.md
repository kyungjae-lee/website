[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > EPI - 5.1. Computing the Parity of a Word (bit manipulation)

# EPI - 5.1. Computing the Parity of a Word (bit manipulation)



## Solutions in C++

### Solution 1

This solution uses **brute-force approach** that iteratively tests the value of each bit while tracking the number of 1s seen so far.

**Complexity Analysis:**

* $T = O(n)$, where $n$ is the word size
* $S = O(1)$

**Solution:**

```cpp
short Parity(unsigned long x)
{
    short result = 0;

    while (x)
    {
        result += (x & 1);
        x >>= 1;
    }

    return result % 2;                                                        
}
```

This can be re-written as follows:

```cpp
short Parity(unsigned long x)
{
    short result = 0;

    while (x)
    {
        result ^= (x & 1);
        x >>= 1;
    }

    return result;
}
```

### Solution 2

This solution improves the Solution 1 by using the technique "Erase the lowest set bit in a word in a single operation" (`x = x & (x - 1)`.

**Complexity Analysis:**

* $T = O(k)$, where $k$ is the number of bits set to 1 (e.g., $k$ = 3 for 10001010~(2)~)
* $S = O(1)$

**Solution:**

```cpp
short Parity(unsigned long x)
{
    short result = 0;
    
    while (x)
    {
        result ^= 1;
        x = x & (x - 1);	// Drop the lowest set bit of x
    }
    
    return result;
}
```

