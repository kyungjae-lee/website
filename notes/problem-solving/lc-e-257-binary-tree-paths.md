[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 257. Binary Tree Paths (DFS)

# LC - E - 257. Binary Tree Paths (DFS)



## Solutions in C++

### Solution 1

This solution uses Depth-First Search (DFS) algorithm and recursion.

Be able to introduce and use new members to the Solution class (e.g., helper functions, variables, etc.)

**Complexity Analysis:**

*  $T = O(n)$ where $n$ is the number of nodes in the given binary tree    
*  $S = O(\log n)$ where $n$ is the number of nodes in the given binary tree (Depth of recursion tree)

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
    vector<string> binaryTreePaths(TreeNode* root) {
        string s;   // will be used to build up each path
        buildPath(root, s);
        return paths;
    }

private: 
    vector<string> paths;

    // helper function
    void buildPath(TreeNode *root, string s)
    {
        // if root does not exist (termination condition 1)
        if (!root)
            return;
        // if root is a leaf (termination condition 2)
        else if (root->left == root->right)  // (!root->left && !root->right)
        {
            s += to_string(root->val);
            paths.push_back(s);
        }
        // if root is a not a leaf 
        else
        {
            s = s + to_string(root->val) + "->";

            // recursive step
            buildPath(root->left, s);
            buildPath(root->right, s);
        }
    }
};
```

