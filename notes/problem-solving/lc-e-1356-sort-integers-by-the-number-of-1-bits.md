[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 1356. Sort Integers by The Number of 1 Bits (struct, qsort, bubble sort)

# LC - E - 1356. Sort Integers by The Number of 1 Bits (struct, qsort, bubble sort)



## Solutions in C

### Solution 1

This solution uses `struct` and the C standard library function `qsort`.

**Complexity Analysis:**

* $T = O(n\log n)$ where $n$ is the length of the given array `arr` (`qsort` governs the time complexity)
* $S = O(n)$ since an array `parr` of size $n$ is used

**Solution:**

```cpp
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */

#include <stdlib.h>	// qsort

typedef struct pair_t
{
    int elem;
    int bitcnt;
} pair_t;

int countBits(int n);
void sort(pair_t *parr, int arrSize);
int comp(const void *pair1, const void *pair2);

int* sortByBits(int* arr, int arrSize, int* returnSize){
    int i;
    *returnSize = arrSize;
    pair_t parr[arrSize];

    for (i = 0; i < arrSize; i++)
    {
        parr[i].elem = arr[i];
        parr[i].bitcnt = countBits(arr[i]);
    }

   	// quick sort (T = O(nlogn))
    qsort(parr, arrSize, sizeof(pair_t), comp);

    for (i = 0; i < arrSize; i++)
        arr[i] = parr[i].elem;
    
    return arr;
}

int countBits(int n)
{
    int count = 0;

    while (n)
    {
        n &= (n - 1);	// clear least set bit of n
        count++;
    }

    return count;
}

int comp(const void *pair1, const void *pair2)
{
    pair_t *p1 = pair1, *p2 = pair2;
    return (p1->bitcnt != p2->bitcnt) ? (p1->bitcnt - p2->bitcnt) : (p1->elem - p2->elem);
}
```



### Solution 2

This solution uses bubble sort.

**Complexity Analysis:**

* $T = O(n^2)$ where $n$ is the length of the given array `arr` (`bubble sort` governs the time complexity)
* $S = O(n)$ since an array `bits` of size $n$ is used

**Solution:**

```cpp
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
void swap(int *arr, int *bits, int i, int j);
int countBits(int n);

int* sortByBits(int* arr, int arrSize, int* returnSize){
    //int *bits = (int *)calloc(*returnSize, sizeof(int)); // array containing the number of bits
    int bits[arrSize];
    int i, j;
    
    *returnSize = arrSize;
    
    for (i = 0; i < arrSize; i++)
        bits[i] = countBits(arr[i]);

    // bubble sort (T = O(n^2))
    for (i = 0; i < arrSize; i++)
    {
        for (j = i + 1; j < arrSize; j++)
        {
            if (bits[i] > bits[j])
                swap(arr, bits, i, j);
            else if (bits[i] == bits[j] && arr[i] > arr[j])
                swap(arr, bits, i, j);
        }
    }

    return arr;
}

void swap(int *arr, int *bits, int i, int j)
{
    int temp;

    temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;

    temp = bits[i];
    bits[i] = bits[j];
    bits[j] = temp;
}

int countBits(int n)
{
    int count = 0;

    while (n)
    {
        n &= (n - 1);
        count++;
    }

    return count;
}
```





