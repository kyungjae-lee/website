[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Tree Traversal (Pre-Order, In-Order, Post-Order) 

# Tree Traversal (Pre-Order, In-Order, Post-Order)  



## Tree Traversal

* Tree traversal refers to the process of systematically visiting and examining all the nodes (elements) of a tree data structure in a specific order. (Pre-Order, In-Order, Post-Order)
* It involves navigating through the nodes, which can be organized hierarchically, and performing actions on them as needed.
* Tree traversal is a fundamental operation for various algorithms and tasks involving trees, such as searching, sorting, and modifying data stored  within the tree.
* Time Complexity of all 3 traversal algorithms: T = O(n), where 'n' is the number of nodes in the tree.
  * In ther worst case, where you have to visit every node of the tree, you visit each node exactly once.

* Space Complexity of all 3 traversal algorithms: : S = O(h), where 'h' is the height of the tree.
  * Each recursive call consumes space on the call stack. The maximum depth of the call stack is determined by the height of the tree.
  * In a balanced binary tree, the height is logarithmic in terms of the number of nodes (O(log n)), while in a skewed tree, it can be as bad as O(n).


### Pre-Order Traversal (n-l-r): T = O(n), S = (h)

* Visits the current node first, then the left subtree, and finally the right subtree.

* Algorithm
  1. If the current node is null, return.
  2. Process the current node (print, store, etc.).
  3. Recursively perform a pre-order traversal on the left subtree.
  4. Recursively perform a pre-order traversal on the right subtree.

* Pseudo-code

  ```plain
  PreOrderTraversal(node):
  	if node is null:
  		return
  	Process(node)
  	PreOrderTraversal(node.left)
  	PreOrderTraversal(node.right)
  ```

### In-Order Traversal (l-n-r): T = O(n), S = (h)

* Visits the left subtree, then the current node, and finally the right subtree.

* Algorithm

  1. If the current node is null, return.
  2. Recursively perform an in-order traversal on the left subtree.
  3. Process the current node (print, store, etc.).
  4. Recursively perform an in-order traversal on the right subtree.

* Pseudo-code

  ```plain
  InOrderTraversal(node):
  	if node is null:
  		return
  	InOrderTraversal(node.left)
  	Process(node)
  	InOrderTraversal(node.right)
  ```

### Post-Order Traversal (l-r-n): T = O(n), S = (h)

* Visits the left subtree, then the right subtree, and finally the current node.

* Algorithm

  1. If the current node is null, return.
  2. Recursively perform a post-order traversal on the left subtree.
  3. Recursively perform a post-order traversal on the right subtree.
  4. Process the current node (print, store, etc.).

* Pseudo-code

  ```plain
  PostOrderTraversal(node):
  	if node is null:
  		return
  	InOrderTraversal(node.left)
  	Process(node)
  	InOrderTraversal(node.right)
  ```

  

## Code (C++)

```cpp
#include <iostream>

using namespace std;

// Definition of a binary tree node
class TreeNode
{
public:
	int value;
	TreeNode *left;
	TreeNode *right;

	// Constructor
	TreeNode(int val) : value(val), left(nullptr), right(nullptr) { }
};

// Processes node
void processNode(TreeNode *node)
{
	cout << node->value << " ";
}

// Pre-Order Traversal
void preOrderTraversal(TreeNode *node)
{
	if (node == nullptr)
		return;

	processNode(node);
	preOrderTraversal(node->left);
	preOrderTraversal(node->right);
}

// In-Order Traversal
void inOrderTraversal(TreeNode *node)
{
	if (node == nullptr)
		return;

	inOrderTraversal(node->left);
	processNode(node);
	inOrderTraversal(node->right);
}

// Post-Order Traversal
void postOrderTraversal(TreeNode *node)
{
	if (node == nullptr)
		return;

	postOrderTraversal(node->left);
	postOrderTraversal(node->right);
	processNode(node);
}

int main(void)
{
	// Construct a sample binary tree
	TreeNode *root = new TreeNode(1);
	root->left = new TreeNode(2);
	root->right = new TreeNode(3);
	root->left->left = new TreeNode(4);
	root->left->right = new TreeNode(5);

	cout << "Pre-Order Traversal: ";
	preOrderTraversal(root);
	cout << endl;

	cout << "In-Order Traversal: ";
	inOrderTraversal(root);
	cout << endl;

	cout << "Post-Order Traversal: ";
	postOrderTraversal(root);
	cout << endl;

	return 0;
}
```

```plain
Pre-Order Traversal: 1 2 4 5 3 
In-Order Traversal: 4 2 5 1 3 
Post-Order Traversal: 4 5 2 3 1 
```

