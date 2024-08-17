[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Selection Sort: T = O(n^2^) , S = O(1) 

# Selection Sort: T = O(n^2^) , S = O(1) 



## Selection Sort

* A sorting algorithm that iteratively selects the smallest (or largest) element from an unsorted portion of the list and places it at the beginning (or end) of the sorted portion. This process is repeated until the entire list is sorted.
* Selection sort has a time complexity of O(n^2^), making it inefficient for large lists.
* Selection sort has a space complexity of O(1). It sorts the array **in place**, that is, It does not create additional copies of the array.

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
SelectionSort(arr):
    n = length(arr)
    for i from 1 to n - 1:
        current = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > current:
            arr[j + 1] = arr[j]
            j = j - 1
        arr[j + 1] = current
```



## Code

```cpp
#include <iostream>

using namespace std;

void selectionSort(int arr[], int size)
{
    for (int i = 0; i < size; i++)
    {
        int minIndex = i;
        for (int j = i + 1; j < size; j++)
        {
            if (arr[j] < arr[minIndex])
                minIndex = j;
        }
        if (minIndex != i)
        {
            int temp = arr[i];
            arr[i] = arr[minIndex];
            arr[minIndex] = temp;
        }
    }
}

int main(void)
{
    int arr[] = {6, 4, 2, 1, 5, 3};
    int size = sizeof(arr) / sizeof(arr[0]);
    
    selectionSort(arr, size);
    
    for (auto val : arr)
        cout << val << " ";
	
    cout << endl;
    
    return 0;
}
```

```plain
1 2 3 4 5 6
```
