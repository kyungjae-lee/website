[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Merge Sort: T = O(n logn) , S = O(n) 

# Merge Sort: T = O(n logn) , S = O(n) 



## Merge Sort

* A **divide-and-conquer** sorting algorithm that works by repeatedly dividing the unsorted list into smaller sublists, sorting those sublists, and then merging them back together to obtain a sorted list. It employs a recursive approach to sorting.

* Merge sort is known for its stable nature and efficient time complexity of O(n logn), making it suitable for larger lists.

  Technical speaking,
  $$
  \text{T} \propto O({n \log n} + n)
  $$
  The term 'n log n' signifies that we iterate through all 'n' items 'log  n' times (representing the recursive depth) during the process of splitting the original array into arrays of size 1 and merging them back into an array of size 'n' recursively.

  The last term, 'n,' accounts for the total number of splits required to break down the original array into arrays of size 1 (and, of course, another 'n' for putting them back together). However, this 'n'  term is non-dominant and thus dropped, as it has a lesser impact on the  overall time complexity.

* Merge sort has space complexity of O(n), where "n" is the number of elements in the input array. This is because merge sort requires additional space to store the temporary subarrays during the merging process.

### Algorithm

1. **Input:** An array of elements to be sorted.
2. **Divide:** Divide the input array into two equal (or nearly equal) halves.
3. **Recursion:** Recursively apply merge sort on each of the two halves until each subarray contains only one element or is empty.
4. **Merge:** Combine the sorted subarrays by merging them. This involves comparing elements from both subarrays and placing them in order in a new temporary array.
5. **Copy Back:** Copy the merged elements from the temporary array back into the original array in their correct sorted order.
6. **Repeat:** Continue this process, dividing, sorting, merging, and copying, until the entire array is sorted.

### Pseudo-code

```plain
MergeSort(arr):
	if length(arr) <= 1:
		return arr
		
	mid = length(arr) / 2
	left = MergeSort(arr[0:mid])
	right = MergeSort(arr[mid:end])
	
	merged = Merge(left, right)
	return merged
	
Merge(left, right):
	result = []
	while left is not empty and right is not empty:
		if left[0] <= right [0]:
			result.append(left[0])
			left = left[1:]
		else
			result.append(right[0])
			right = right[1:]
			
	// Append any remaining elements
	result.extend(left)
	result.extend(right)
	return result
```



## Code (C++)

```cpp
#include <iostream>
#include <vector>

using namespace std;

// Merge function to combine two sorted arrays
vector<int> merge(vector<int>& left, vector<int>& right)
{
    vector<int> merged;
    int leftIdx = 0, rightIdx = 0;
    
    while (leftIdx < left.size() && rightIdx < right.size())
    {
        if (left[leftIdx] <= right[rightIdx])
        {
            merged.push_back(left[leftIdx]);
            leftIdx++;
        }
        else
        {
            merged.push_back(right[rightIdx]);
            rightIdx++;
        }
    }
    
    while (leftIdx < left.size())
    {
        merged.push_back(left[leftIdx]);
        leftIdx++;
    }
    
    while (rightIdx < right.size())
    {
        merged.push_back(right[rightIdx]);
        rightIdx++;
    }
    
    return merged;
}

// Merge sort function
vector<int> mergeSort(vector<int>& arr)
{
    if (arr.size() <= 1)
        return arr;
    
    int mid = arr.size() / 2;
    vector<int> left(arr.begin(), arr.begin() + mid);	// [0, mid)
    vector<int> right(arr.begin() + mid, arr.end());	// [mid, end) == [mid, last idx]
    
    left = mergeSort(left);
    right = mergeSort(right);
    
    return merge(left, right);
}

int main(void)
{
    vector<int> arr = {6, 4, 2, 1, 5, 3};
    vector<int> sortedArr = mergeSort(arr);
    
    for (auto val : sortedArr)
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
#include <stdlib.h>

// Merge function to combine two sorted arrays
void merge(int arr[], int left[], int leftSize, int right[], int rightSize)
{
    int leftIdx = 0, rightIdx = 0, idx = 0;
    
    while (leftIdx < leftSize && rightIdx < rightSize)
    {
        if (left[leftIdx] <= right[rightIdx])
        {
            arr[idx] = left[leftIdx];
            leftIdx++;
        }
        else
        {
            arr[idx] = right[rightIdx];
            rightIdx++;
        }
        
        idx ++;
    }
    
    while (leftIdx < leftSize)
    {
        arr[idx] = left[leftIdx];
        leftIdx++;
        idx++;
    }
    
    while (rightIdx < rightIdx)
    {
        arr[idx] = right[rightIdx];
        rightIdx++;
        idx++;
    }
}

// Merge sort function
void mergeSort(int arr[], int size)
{
    if (size <= 1)
        return;
    
    int mid = size / 2;
    int *left = (int *)malloc(mid * sizeof(int));
    int *right = (int *)malloc((size - mid) * sizeof(int));
    
    for (int i = 0; i < mid; i++)
 		left[i] = arr[i];
    
    for (int i = mid; i < size; i++)
        right[i - mid] = arr[i];
    
    mergeSort(left, mid);
    mergeSort(right, size - mid);
    merge(arr, left, mid, right, size - mid);
    
    free(left);
    free(right);
}

int main(void)
{
    int arr[] = {6, 4, 2, 1, 5, 3};
    int size = sizeof(arr) / sizeof(arr[0]);
    
	mergeSort(arr, size);
    
    for (int i = 0; i < size; i++)
        printf("%d ", arr[i]);
	
    puts("");
    
    return 0;
}
```

```plain
1 2 3 4 5 6
```

