[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 762. Prime Number of Set Bits in Binary Representation (bit manipulation)

# LC - E - 762. Prime Number of Set Bits in Binary Representation (bit manipulation)



## Solutions in C

### Solution 1

This solution uses bitwise operations.

**Complexity Analysis:**

* $T = O(mn)$, where $m$ is the number of numbers in the inclusive range `[left, right]`, and $n$ is the average number of set bits in the numbers 

  $\to$ Not sure! Need to double-check!

* $S = O(1)$ 

**Solution:**

```cpp
bool isPrime(int n);
int countBits(int n);

int countPrimeSetBits(int left, int right){
    int count = 0;

    while (left <= right)
    {
        if (isPrime(countBits(left)))
            count++;

        left++;
    }

    return count;
}

int countBits(int n)
{
    int count = 0;

    while (n)
    {
        n &= (n - 1);	// clear lowest set bit
        count++;
    }

    return count; 
}

bool isPrime(int n)
{
    int i;

    // 1 is not a prime number
    if (n == 1)
        return false;

    for (i = 2; i < n; i++)
    {
        if (n % i == 0)
            return false;
    }

    return true;
}
```

