[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Coding Interview Questions</a> > LC - E - 108. Convert Sorted Array to Binary Search

# LC - E - 108. Convert Sorted Array to Binary Search




## Solutions in C++

### Solution 1

To create a height-balanced binary search tree (BST) from a sorted  array, we need to maintain an equal depth across the tree, so both left  and right subtrees are as close in height as possible. The best way to  achieve this is by choosing the middle element of the array as the root, ensuring the left half of the array will form the left subtree and the  right half will form the right subtree. Recursively applying this  approach ensures the resulting tree is both height-balanced and sorted  according to BST properties.

**Approach**

1. Choose the middle element as root:

   For each segment of the array, pick the middle element. This divides the array into two halves that will become the left and right subtrees.

2. Recursive division:

   - Recursively build the left subtree using the left half of the array.
   - Recursively build the right subtree using the right half of the array.

3. Base case:

   If the current segment is empty, return `null`, as there are no more elements to process.

**Complexity Analysis:**

* $T=O(n)$ 

* $S=O(n)$ 

**Solution:**

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return helper(nums, 0, nums.size() - 1);
    }

private:
    TreeNode* helper(vector<int>& nums, int left, int right)
    {
        if (left > right)
            return nullptr;
        
        int mid = left + (right - left) / 2;

        TreeNode *root = new TreeNode(nums[mid]);

        root->left = helper(nums, left, mid - 1);
        root->right = helper(nums, mid + 1, right);

        return root;
    }
};
```

> L24: This is to avoid `int` overflow in case of very large values. (e.g., during the calculation of `(left + right) / 2`, int overflow can occur.)
>
> This approach ensures the creation of a balanced BST with O(n)  complexity, as each element in the array is processed once. By choosing  the middle element as the root and recursively applying the same process to each half of the array, we ensure the final tree is balanced and  sorted as required for a BST.
