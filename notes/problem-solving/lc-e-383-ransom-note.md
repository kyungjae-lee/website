[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 383. Ransom Note (map)

# LC - E - 383. Ransom Note (map)



## Solutions in C

### Solution 1

This solution uses C implementation of `map` using `array`.

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the length of the given string `magazine`
* $S = O(1)$ since a constant-size array (`map`) is used

**Solution:**

```cpp
#define MAX 26

bool canConstruct(char * ransomNote, char * magazine){
    int map[MAX] = {0};
    int i, idx;

    // insert characters in 'magazine' into 'map'
    for (i = 0; i < strlen(magazine); i++)
    {
        idx = magazine[i] - 'a';
        map[idx]++;
    }

    // consume letters in 'ransomNote' from 'map'
    for (i = 0; i < strlen(ransomNote); i++)
    {
        idx = ransomNote[i] - 'a';

        // if cannot consume, return false
        if (map[idx]-- == 0)
            return false;
    }

    return true; 
}
```

