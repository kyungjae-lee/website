[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Bubble Sort: T = O(n^2^) , S = O(1) 

# Bubble Sort: T = O(n^2^) , S = O(1) 



## Bubble Sort

* **Bubble Sort** is a simple sorting algorithm that:
  - Repeatedly **compares adjacent elements** in an array
  - **Swaps them if they are in the wrong order**
  - After each full pass, the **largest element "bubbles up" to the end**
* Bubble sort has a time complexity of O(n^2^), making it inefficient for large lists.
* Bubble sort has a space complexity of O(1). It sorts the array **in place**, that is, It does not create additional copies of the array.



## Implementation (C++)

### Source (`bubble_sort.cpp`)

```cpp
#include <iostream>
#include <vector>

void bubble_sort(std::vector<int> &arr)
{
    int n = arr.size();
    bool swapped;
    
    for (int i = 0; i < n - 1; ++i)
    {
        swapped = false;
        
        for (int j = 0; j < n - i - 1; ++j)
        {
            if (arr[j] > arr[j + 1])
            {
                std::swap(arr[j], arr[j + 1]);
                swapped = true;
            }
        }
        
        // Optimization: if no swaps were made, the array is already sorted
        if (!swapped)
        {
            break;
        }
    }
}

int main(int argc, char *argv[])
{
	std::vector<int> arr = {6, 4, 2, 1, 5, 3};

	bubble_sort(arr);

	for (auto &elem : arr)
	{
		std::cout << elem << " ";
	}

	std::cout << std::endl;

	return 0;
}
```

```plain
1 2 3 4 5 6
```



## Implementation (C)

### Source (`bubble_sort.c`)

```cpp
#include <stdio.h>
#include <stdbool.h>

void bubble_sort(int arr[], const int size)
{
	int i, j, temp;
	bool swapped;


	for (i = 0; i < size - 1; ++i)
	{
		swapped	= false;

		for (j = 0; j < size - i - 1; ++j)
		{
			if (arr[j] > arr[j + 1])
			{
				// Swap
				temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;

				swapped = true;
			}
		}

		// Optimization: if no swaps were made, the array is already sorted
		if (!swapped)
		{
			break;
		}
	}
}

int main(int argc, char *argv[])
{
	int arr[] = {6, 4, 2, 1, 5, 3};
	int size = sizeof(arr) / sizeof(arr[0]);
	int i;

	bubble_sort(arr, size);

	for (i = 0; i < size; ++i)
	{
		printf("%d ", arr[i]);
	}

	puts("");

	return 0;
}
```

```plain
1 2 3 4 5 6
```
