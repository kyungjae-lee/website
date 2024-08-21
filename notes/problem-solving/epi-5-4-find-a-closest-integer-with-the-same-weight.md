[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > EPI - 5.4. Find a Closest Integer with the Same Weight (bit manipulation)

# EPI - 5.4. Find a Closest Integer with the Same Weight (bit manipulation)



## Solutions in C

### Solution 1

This solution uses bit swapping technique using shift and `xor` operation. 

The key is to finding the algorithm: Swap the two rightmost consecutive bits that differ.

It is assumed that `x` is not 0, or all 1s.

**Complexity Analysis:**

* $T = O(n)$, where $n$ is the width of the given integer `x`
* $S = O(1)$ 

**Solution:**

```cpp
unsigned long ClosestIntSameBitCount(unsigned long x)
{
    int i;
    
    for (i = 0; i < 63; i++)
    {
        if (((x >> i) & 1) != ((x >> (i + 1)) & 1))
        {
            x ^= ((1UL << i) | (1UL << (i + 1))); 	// swap bits
            break;
        }
    }
    
    return x;
}
```

