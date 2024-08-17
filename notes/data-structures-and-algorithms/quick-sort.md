[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Quick Sort: T = O(n logn) , S = O(n) 

# Quick Sort: T = O(n logn) , S = O(n) 



## Quick Sort

* A widely used sorting algorithm that employs a **divide-and-conquer** strategy. It selects a "pivot" element from an array  and partitions the other elements into two subarrays, one containing elements less than the pivot and the other containing elements greater than the pivot. This process is recursively applied to the subarrays, effectively sorting the entire array. 

* Quick sort has an average time complexity of O(n log n) and often outperforms other sorting algorithms due to its efficient partitioning technique.

* The space complexity of Quick Sort is generally O(log n), where "n" is the number of elements in the array being sorted.

* Quick sort has space complexity of O(n), where "n" is the number of elements in the input array.


### Algorithm

1. **Input:** An array of elements to be sorted.
2. **Choose Pivot:** Select a pivot element from the array. The pivot can be chosen using various methods, such as selecting the first, last, or middle element.
3. **Partitioning:** Rearrange the elements in the array such that all elements less than the pivot are moved to its left and all elements greater than the pivot are moved to its right. The pivot element is now in its final sorted position.
4. **Recursion:** Recursively apply quick sort to the subarrays on the left and right of the pivot. These subarrays are formed by the partitioning step.
5. **Combine:** No combining step is needed, as the array is already sorted in place after the partitioning and recursive sorting steps.
6. **Base Case:** The base case for the recursion is when the subarray has only one element or is empty. In such cases, the array is considered sorted.

### Pseudo-code

```plain
QuickSort(arr, begIdx, endIdx):
	if begIdx < endIdx:
		pivotIdx = Partition(arr, begIdx, endIdx)
		QuickSort(arr, begIdx, pivotIdx - 1)
		QuickSort(arr, pivotIdx + 1, endIdx)
		
Partition(arr, begIdx, endIdx):
	pivot = arr[endIdx]
	gtIdx = begIdx - 1
	for i from lbegIdxow to endIdx-1:
		if arr[i] <= pivot:
			gtIdx = gtIdx+1
			swap(arr[gtIdx], arr[i])
	swap(arr[gtIdx+1], arr[endIdx])
	return gtIdx+1
```



## Code (C++)

```cpp
#include <iostream>
#include <vector>

using namespace std;

// Function to partition the array
int partition(vector<int>& arr, int begIdx, int endIdx)
{
	int pivot = arr[endIdx];
	int gtIdx = begIdx - 1;	// To mark the first element that is greater than the pivot
    
	for (int i = begIdx; i < endIdx; i++)
	{
		if (arr[i] <= pivot)
		{
			gtIdx++;
			swap(arr[gtIdx], arr[i]);
		}
    }
    
    // At this point, arr[gtIdx] does not contain the first element that is greater
    // than the pivot due to the swap operation previously performed. Rather, it should 
    // contain the last element that is smaller than the pivot.
	swap(arr[gtIdx + 1], arr[endIdx]);
    
    // Now, the pivot is placed in the index 'greaterIdx + 1'.
	return gtIdx + 1;
}

// Quick Sort function
void quickSort(vector<int>& arr, int begIdx, int endIdx)
{
	if (begIdx < endIdx)
	{
		int pivotIdx = partition(arr, begIdx, endIdx);
		quickSort(arr, begIdx, pivotIdx - 1);
		quickSort(arr, pivotIdx + 1, endIdx);
	}
}

int main(void)
{
	vector<int> arr = {6, 4, 2, 1, 5, 3};
	quickSort(arr, 0, arr.size() - 1);
    
	for (auto val : arr)
		cout << val << " ";
	
	cout << endl;
    
	return 0;
}
```

```plain
1 2 3 4 5 6
```



## Code (C)

```c
#include <stdio.h>

// Function to swap two elements
void swap(int *a, int *b)
{
	int temp = *a;
	*a = *b;
	*b = temp;
}

// Function to partition the array
int partition(int arr[], int begIdx, int endIdx)
{
	int pivot = arr[endIdx];
	int gtIdx = begIdx - 1;
	    
	for (int i = begIdx; i < endIdx; i++)
	{
		if (arr[i] <= pivot)
		{
			gtIdx++;
			swap(&arr[gtIdx], &arr[i]);
		}
	}
    
	swap (&arr[gtIdx + 1], &arr[endIdx]);
	return gtIdx + 1;
}

// Quick Sort function
void quickSort(int arr[], int begIdx, int endIdx)
{
	if (begIdx < endIdx)
	{
		int pivotIdx = partition(arr, begIdx, endIdx);
		quickSort(arr, begIdx, pivotIdx - 1);
		quickSort(arr, pivotIdx + 1, endIdx);
	}
}

int main(void)
{
	int arr[] = {6, 4, 2, 1, 5, 3};
	int size = sizeof(arr) / sizeof(arr[0]);
    
	quickSort(arr, 0, size - 1);
    
	for (int i = 0; i < size; i++)
		printf("%d ", arr[i]);
    
	puts("");
    
	return 0;
}
```

```plain
1 2 3 4 5 6
```
