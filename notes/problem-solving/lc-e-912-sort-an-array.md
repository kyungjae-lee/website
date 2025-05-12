[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 912. Sort an Array

# LC - E - 912. Sort an Array




## Solutions in C++

### Solution 1

Merge sort.

**Complexity Analysis:**

* T = O(n log n)

* S = O(n)

**Solution:**

```cpp
class Solution {
public:
    vector<int> sortArray(vector<int>& nums) {
        // base case        
        if (nums.size() <= 1)
            return nums;
        
        int mid = nums.size() / 2;
        
        // split the original array into halves
        vector<int> left(nums.begin(), nums.begin() + mid);
        vector<int> right(nums.begin() + mid, nums.end());

        sortArray(left);
        sortArray(right);
        merge(nums, left, right);

        return nums;
    }

private:
    void merge(vector<int> &arr, const vector<int> &left, const vector<int> &right)
    {
        int i = 0;  // index for the original array
        int l = 0;  // index for the left subarray
        int r = 0;  // index for the right subarray

        // memrge elements of the two subarrays
        while (l < left.size() && r < right.size())
        {
            arr[i++] = (left[l] <= right[r]) ? left[l++] : right[r++];
        }

        // copy remaining elements (only one subarray may have leftovers)
        while (l < left.size())
        {
            arr[i++] = left[l++];
        }

        while (r < right.size())
        {
            arr[i++] = right[r++];
        }
    }
};
```
