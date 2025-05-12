[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Merge Sort: T = O(n logn) , S = O(n) 

# Merge Sort: T = O(n logn) , S = O(n) 



## Introduction

* **Merge Sort** is a **divide-and-conquer** sorting algorithm that:

  1. Recursively **divides** the array into halves until single elements remain.
  2. Then **merges** those halves back together in **sorted order**.

* **Merge sort** is a **stable**, efficient sorting algorithm with a time complexity of **O(n log n)**, making it well-suited for large datasets.

  Technically:
  $$
  T(n)=O(nlog⁡n+n)
  $$
  

  - The **`n log n`** term comes from dividing the array in half `log n` times and processing `n` elements at each level during merging.
  - The extra **`n`** term accounts for the total number of splits, but it’s **non-dominant**, so it's omitted in the final complexity.

* **Space complexity** is **O(n)** because merge sort needs additional memory to hold temporary subarrays during merging.



## Implementation (C++)

### Source (`merge_sort.cpp`)

```cpp
#include <iostream>
#include <vector>

// merge two sorted halves into original array
void merge(std::vector<int> &arr, const std::vector<int> &left, const std::vector<int> &right)
{
	int i = 0;	// index for origiinal array
	int l = 0;	// index for left subarray
	int r = 0;	// index for right subarray

	// merge elements of two subarrays
	while (l < left.size() && r < right.size())
	{
		if (left[l] <= right[r])
		{
			arr[i++] = left[l++];
		}
		else
		{
			arr[i++] = right[r++];
		}
	}

	// copy remaining elements (only one subarray may ahve leftovers)
	while (l < left.size())
	{
		arr[i++] = left[l++];
	}
	while (r < right.size())
	{
		arr[i++] = right[r++];
	}
}

// recursive merge sort
void merge_sort(std::vector<int> &arr)
{
	// base case
	if (arr.size() <= 1)
	{
		return;
	}

	int mid = arr.size() / 2;

	std::vector<int> left(arr.begin(), arr.begin() + mid);	// [arr[0], arr[mid - 1]] 
	std::vector<int> right(arr.begin() + mid, arr.end());	// [arr[mid], arr[arr.size() - 1]]

	// recursive case
	merge_sort(left);
	merge_sort(right);
	merge(arr, left, right);
}

int main(int argc, char *argv[])
{
    std::vector<int> arr = {5, 2, 9, 1, 5, 6};
    merge_sort(arr);
    
    for (auto &elem : arr)
	{
        std::cout << elem << " ";
	}
	
    std::cout << std::endl;
    
    return 0;
}
```

```plain
1 2 5 5 6 9
```



## Implementation (C)

### Source (`merge_sort.c`)

```c
#include <stdio.h>
#include <stdlib.h>

// merge two sorted subarrays into the original array
void merge(int *arr, const int *left, const int l_size, const int *right, const int r_size)
{
	int i = 0;	// index for original array	
	int l = 0;	// index for left subarray	
	int r = 0;	// index for right subarray	

	// merge elements of two subarrays
	while (l < l_size && r < r_size)
	{
		if (left[l] <= right[r])
		{
			arr[i++] = left[l++];
		}
		else
		{
			arr[i++] = right[r++];
		}
	}

	// copy remaining elements (only one subarray may ahve leftovers)
	while (l < l_size)
	{
		arr[i++] = left[l++];
	}

	while (r < r_size)
	{
		arr[i++] = right[r++];
	}
}

// recursive merge sort
void merge_sort(int *arr, int size)
{
	if (size <= 1)
	{
		return;
	}

	int mid = size / 2;

	// allocate and copy left and right halves
	int *left = (int *)malloc(mid * sizeof(int));
	int *right = (int *)malloc((size - mid) * sizeof(int));

	for (int l = 0; l < mid; ++l)
	{
		left[l] = arr[l];
	}

	for (int r = mid; r < size; ++r)
	{
		right[r - mid] = arr[r];
	}

	merge_sort(left, mid);
	merge_sort(left, size - mid);
	merge(arr, left, mid, right, size - mid);

	free(left);
	free(right);
}

int main(int argc, char *argv[])
{
	int arr[] = {5, 2, 9, 1, 5, 6};
	int size = sizeof(arr) / sizeof(arr[0]);

	merge_sort(arr, size);

	for (int i = 0; i < size; ++i)
	{
		printf("%d ", arr[i]);
	}

	puts("");
	
	return 0;
}
```

```plain
1 2 5 5 6 9
```
