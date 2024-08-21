[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 404. Sum of Left Leaves (DFS)

# LC - E - 404. Sum of Left Leaves (DFS)



## Solutions in C++

### Solution 1

This solution uses Depth-First Search (DFS) algorithm.

The strategy is to perform DFS and accumulate the values of the left leaves only.

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the number of nodes in the given binary tree
* $S = O(\log n)$ where $n$ is the number of nodes in the given binary tree (Depth of recursion tree)

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
    int sumOfLeftLeaves(TreeNode* root) {
        int sum = 0;
        helper(root, sum, 0);
        return sum;
    }

private:
    void helper(TreeNode *curr, int &sum, bool isLeft)
    {
        // terminating condition 1
        if (curr == nullptr)
            return;

        // terminating condition 2
        if (curr->left == curr->right)
        {
            sum += (curr->val * isLeft);
            return;
        }
        
        // recursive step
        helper(curr->left, sum, 1);
        helper(curr->right, sum, 0);
    }
};
```



### Solution 2

This solution does not use helper function or additional variable. 

**Complexity Analysis:**

* $T = O(n)$ where $n$ is the number of nodes in the given binary tree
* $S = O(\log n)$ where $n$ is the number of nodes in the given binary tree (Depth of recursion tree)

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
    int sumOfLeftLeaves(TreeNode* root) {
        if (!root)
            return 0;

        // if left node is a leaf
        if (root->left && !root->left->left && !root->left->right)
            return root->left->val + sumOfLeftLeaves(root->right);
        
        // otherwise
        return sumOfLeftLeaves(root->left) + sumOfLeftLeaves(root->right);
    }
};
```

