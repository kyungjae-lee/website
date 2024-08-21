[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 2595. Number of Even and Odd Bits (bit manipulation)

# LC - E - 2595. Number of Even and Odd Bits (bit manipulation)



## Solutions in C

### Solution 1

This solution uses bitwise operations.

**Complexity Analysis:**

* $T = O(\log n)$ where $n$ is the given integer value
* $S = O(1)$

**Solution:**

```cpp
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* evenOddBit(int n, int* returnSize){
    *returnSize = 2;
    int *answer = (int *)calloc(*returnSize, sizeof(int));
    int i;
    int temp = n;
    
    // count even
    for (i = 0; 2 * i < 32; i++)
        answer[0] += (temp >> (2 * i) & 1);

    // count odd
    for (i = 0; 2 * i + 1 < 32; i++)
        answer[1] += (n >> (2 * i + 1) & 1);

    return answer;
}
```

### Solution 2

This solution uses bitwise operations. (e.g., Bit masking)

**Complexity Analysis:**

* $T = O(k)$ where $k$ is the number of bits set to 1 in the given integer `n` (On average more efficient than $O(n)$)
* $S = O(1)$

**Solution:**

```cpp
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* evenOddBit(int n, int* returnSize){
    unsigned int bitmask_odd = 0xAAAAAAAA; 	// 01010101 01010101 01010101 01010101
    unsigned int bitmask_even = 0x55555555;	// 10101010 10101010 10101010 10101010
    int ncopy = n;
    *returnSize = 2;

    int *answer = (int *)calloc(2, sizeof(int));

    // count 'even'
    ncopy &= bitmask_even;
    while (ncopy)
    {
        ncopy &= (ncopy - 1);
        answer[0]++;
    }

    // count 'odd'
    n &= bitmask_odd;
    while (n)
    {
        n &= (n - 1);	// drop the lowest set bit of 'n'
        answer[1]++;
    }

    return answer;
}
```

