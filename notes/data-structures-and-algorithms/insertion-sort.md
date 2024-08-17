[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Insertion Sort: T = O(n^2^) , S = O(1) 

# Insertion Sort: T = O(n^2^) , S = O(1) 



## Insertion Sort

* A simple sorting algorithm that builds the final sorted array one element at a time. It iterates through the input list and, in each iteration, compares the current element with the elements to its left and inserts it into the correct position within the already sorted portion of the array. This process is repeated until the entire array is sorted.
* Insertion sort has a time complexity of O(n^2^) but performs better on partially sorted or small lists compared to other quadratic time complexity algorithms.
* Insertion sort has a space complexity of O(1). It sorts the array **in place**, that is, It does not create additional copies of the array.

### Algorithm

1. **Input:** An array of elements to be sorted.
2. **Loop:** Start a loop that iterates from the second element to the last element of the array.
3. **Current Element:** For the current iteration, store the value of the element to be inserted into the sorted portion.
4. **Inner Loop:** Within the loop, iterate from the current element's position back to the beginning of the array (or until a smaller element is found).
5. **Shift and Insert:** While iterating in the inner loop, if the current element is smaller than the element being compared, shift the compared element to the right to make space for the current element.
6. **Insert Current Element:** Once a suitable position is found in the inner loop, insert the current element into that position.
7. **Repeat:** Continue this process for each element in the array until the entire array is sorted.

### Pseudo-code

```plain
InsertionSort(arr):
	n = length(arr)
	for i from 1 to n-1:
		current = arr[i]
		j = i-1
		while j > -1 and arr[j] > current:
			arr[j+1] = arr[j]
			j = j-1
		arr[j+1] = current
```

> L6: The order in which you write the two condition statements of the while loop is very important. Switching the order will cause a run-time error for referencing `arr[-1]`. 



## Code

```cpp
#include <iostream>

using namespace std;

void insertionSort(int arr[], int size)
{
	for (int i = 1; i < size; i++)	// Loop: 2nd element - last element
    {
        int curr = arr[i];			// curr: element to find correct spot in this pass
        int j = i - 1;				
        
        while (j > -1 && arr[j] > curr)
        {
            arr[j + 1] = arr[j];	// Move an array element to the right
            j = j - 1;
        }
        
        arr[j + 1] = curr;	// Insert the current element into the correct spot
    }
}

int main(void)
{
    int arr[] = {6, 4, 2, 1, 5, 3};
    int size = sizeof(arr) / sizeof(arr[0]);
    
    insertionSort(arr, size);
    
    for (auto val : arr)
        cout << val << " ";
	
    cout << endl;
    
    return 0;
}
```

```plain
1 2 3 4 5 6
```
