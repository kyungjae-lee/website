[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 2220. Minimum Bit Flips to Convert Number

# LC - E - 2220. Minimum Bit Flips to Convert Number (bit manipulation)



## Solutions in C

### Solution 1

This solution uses bitwise operations.

**Complexity Analysis:**

* $T = O(k)$ where $k$ is the number of bits set to 1 in `start ^ goal` 
* $S = O(1)$ 

**Solution:**

```cpp
int minBitFlips(int start, int goal){
    int count = 0;

    // 1. XOR two numbers -> result will contain only the different bits set
    start ^= goal;

    // 2. clear least set bit one by one and count them.
    while (start)
    {
        start &= (start - 1); // clear the lowest set bit of 'start'
        count++;
    }

    return count;
}
```

