[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Coding Interview Questions</a> > Find Anagrams

# Find Anagrams



## Solution

```c
#include <stdio.h>
#include <string.h>
#include <stdbool.H>

bool is_anagram(const char *s1, const char *s2)
{
	if (strlen(s1) != strlen(s2))
	{
		return false;
	}

	int count[256] = {0};	// ASCII

	while (*s1)
	{
		count[(unsigned char)*s1++]++;
		count[(unsigned char)*s2++]--;
	}

	for (int i = 0; i < 256; i++)
	{
		if (count[i] != 0)
		{
			return false;
		}
	}

	return true;
}
```



## Test

```c
int main(int argc, char *argv[])
{
	char s1[64] = {0};
	char s2[64] = {0};

	printf("Enter string 1: ");
	fgets(s1, sizeof(s1), stdin);
	printf("Enter string 2: ");
	fgets(s2, sizeof(s2), stdin);

	if (is_anagram(s1, s2))
	{
		printf("Anagram\n");
	}
	else
	{
		printf("Not Anagram\n");
	}

	return 0;
}
```

