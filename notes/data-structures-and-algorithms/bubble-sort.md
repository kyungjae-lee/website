[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Bubble Sort: T = O(n^2^) , S = O(1) 

# Bubble Sort: T = O(n^2^) , S = O(1) 



## Bubble Sort

* A simple sorting algorithm that repeatedly steps through a list of elements, compares adjacent elements, and swaps them if they  are in the wrong order. This process is repeated for each pair of adjacent elements until the entire list is sorted.
* Gets its name from the way smaller elements "bubble" to the beginning of the list with each pass.
* Bubble sort has a time complexity of O(n^2^), making it inefficient for large lists.
* Bubble sort has a space complexity of O(1). It sorts the array **in place**, that is, It does not create additional copies of the array.

### Algorithm

1. **Input:** An array of elements to be sorted.
2. **Loop:** Start a loop that will run for the total number of elements in the array.
3. **Inner Loop:** Within the loop, iterate through the array from the beginning to the second-to-last element.
4. **Compare and Swap:** For each pair of adjacent elements in the inner loop, compare them. If the elements are in the wrong order (i.e., the current element is greater than the next element), swap them.
5. **Repeat:** Continue this process for each pair of adjacent elements in the array for each iteration of the outer loop.
6. **One Pass Complete:** After each pass of the inner loop, the largest (or smallest, depending on the sorting order) element "bubbles up" to the end (or beginning) of the array.
7. **Reduce Inner Loop:** In the next iteration of the outer loop, you can reduce the range of the inner loop by one, as the largest (or smallest) element is already in its correct position.
8. **Repeat Outer Loop:** Repeat steps 3 to 7 for the remaining unsorted portion of the array until the entire array is sorted.

### Pseudo-code

```plain
BubbleSort(arr):
    n = length(arr)
    for i from 0 to n-1:
        for j from 0 to n-i-2:
            if arr[j] > arr[j+1]:
                swap(arr[j], arr[j+1])
```



## Code

```cpp
#include <iostream>

using namespace std;

void bubbleSort(int arr[], int size)
{
    for (int i = 0; i < size; i++)
    {
        for (int j = 0; j < size - i - 1; j++)
        {
            if (arr[j] > arr[j + 1])
            {
                // Swap
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}

int main(void)
{
    int arr[] = {6, 4, 2, 1, 5, 3};
    int size = sizeof(arr) / sizeof(arr[0]);
    
    bubbleSort(arr, size);
    
    for (auto val : arr)
        cout << val << " ";
	
    cout << endl;
    
    return 0;
}
```

```plain
1 2 3 4 5 6
```
