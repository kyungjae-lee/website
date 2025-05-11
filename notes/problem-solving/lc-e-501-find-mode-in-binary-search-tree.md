[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 501. Find Mode in Binary Search Tree

# LC - E - 501. Find Mode in Binary Search Tree




## Solutions in C++

### Solution 1

**Complexity Analysis:**

* T = O(n log n) 
  * `inorder(root)` - O(n log n), where 'n' = number of nodes
    * Visits every node exactly once.

    * For each node, it does a `map[val]++` → this is a logarithmic operation due to `std::map`.

  * `for (auto &elem : m)` to find max frequency - O(n)
    * There are at most 'n' unique values.

  * `for (auto &elem : m)` to collect modes - O(n)
    * There are at most 'n' unique values.

* S = O(n) 
  * Map - O(n)
  * Output vector - O(n)
  * Call stack - Recursive in-order traversal 
    * For a balanced tree: O(log n), for skewed tree: O(n)


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
    vector<int> findMode(TreeNode* root) {
        vector<int> v;

        if (root == nullptr)
        {
            v.push_back(0);
            return v;
        }

        // populate the map
        inorder(root);

        // find the maximum occurrence
        int max = 0;
        for (auto &elem : m)
        {
            if (elem.second > max)
                max = elem.second;
        }

        // populate the vector with the key(s) with maximum occurrence
        for (auto &elem : m)
        {
            if (elem.second == max)
                v.push_back(elem.first);
        }

        return v;
    }

private:
    void inorder(TreeNode *root)
    {
        // recursively traverse the passed tree and populate the map

        // terminating condition
        if (root == nullptr)
            return;

        // recursive steps
        inorder(root->left);
        m[root->val]++;
        inorder(root->right);

        return;
    }

    std::map<int, int> m;
};
```
