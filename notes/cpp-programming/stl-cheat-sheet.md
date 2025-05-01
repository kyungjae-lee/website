[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > STL Cheat Sheet

# STL Cheat Sheet



## Pair (`pair`)

* **Definition**: Combines two values into one object (can be different types).

* **Use Case**: Useful for returning multiple values, sorting by custom keys, or using as keys in maps.

* **Underlying Structure**: Simple struct

* **Declaration**:

  ```cpp
  #include <pair>
  
  std::pair<int, int> p = {1, 2};
  ```

* **Key Operations**:

  ```cpp
  p.first;	// Access the first element
  p.second;	// Access the second element
  ```

* **Iterators**:

  `std::pair` **does not have an iterator**.

  `std::pair<T1, T2>` is a **simple struct-like container** that holds exactly **two values**: `first` and `second`. It’s not a sequence container like `vector`, `deque`, or `list`, so iteration doesn’t apply.

* **Time Complexity**: Access: **O(1)**



## Vector (`vector`)

* **Definition**: Dynamic array with contiguous storage.

* **Use Case**: Ideal for indexed access and resizable sequences.

* **Underlying Structure**: Dynamic array (heap-allocated)

* **Declaration**:

  ```cpp
  #include <vector>
  
  std::vector<int> v = {1, 2, 3, 4, 5};
  ```

* **Key Operations**:

  ```cpp
  v.push_back(x);		// Add element at the back
  v.pop_back();       // Remove last element
  v[i];               // Access i-th element (no bounds check)
  v.at(i);            // Access i-th element with bounds check
  v.front();          // Access first element
  v.back();           // Access last element
  v.insert(it, x);    // Insert x before iterator it
  v.erase(it);        // Erase element at iterator it
  v.begin(), v.end();	// Iterators for traversal
  v.size();           // Number of elements
  v.empty();          // Check if vector is empty
  v.clear();          // Remove all elements
  v.resize(n);        // Resize the vector to n elements
  v.reserve(n);       // Reserve space for n elements (prevents reallocations)
  ```

* **Iterators**:

  `std::vector` provides full iterator support, including random access.

  ```cpp
  std::vector<T>::iterator it;
  
  v.begin();      // Iterator to the first element
  v.end();        // Iterator past the last element
  ```

  ```cpp
  std::vector<T>::reverse_iterator rit;
  
  v.rbegin();     // Reverse iterator to the last element
  v.rend();       // Reverse iterator past the first element
  ```

  ```cpp
  std::vector<T>::const_iterator cit;
  
  v.cbegin();     // Const iterator to the first element
  v.cend();       // Const iterator past the last element
  ```

  ```cpp
  std::vector<T>::const_reverse_iterator crit;
  
  v.crbegin();    // Const reverse iterator to the last element
  v.crend();      // Const reverse iterator past the first element
  ```

* **Time Complexities**:

  | Operation          | Time  |
  | ------------------ | ----- |
  | Access (by index)  | O(1)  |
  | push_back          | O(1)* |
  | pop_back           | O(1)  |
  | insert/erase (pos) | O(n)  |
  | search             | O(n)  |

  > *Amortized O(1)



## List (`list`)

* **Definition**: Doubly linked list with efficient insert/delete at both ends.

* **Use Case**: Insertions/deletions from anywhere in constant time without shifting.

* **Underlying Structure**: Doubly linked list

* **Declaration**:

  ```cpp
  #include <list>
  
  std::list<int> ls;
  ```

* **Key Operations**:

  ```cpp
  ls.push_back(x);		// Add element at the back
  ls.push_front(x);		// Add element at the front
  ls.pop_back();			// Remove last element
  ls.pop_front();			// Remove first element
  ls.insert(it, x);       // Insert x before iterator it
  ls.erase(it);           // Erase element at iterator it
  ls.begin(), ls.end();   // Iterators for traversal
  ls.size();              // Number of elements
  ls.empty();             // Check if list is empty
  ls.clear();             // Remove all elements
  ls.sort();              // Sort the list
  ls.reverse();           // Reverse the list
  ```

* **Iterators**:

  `std::list` provides full iterator support, just like `std::vector` and `std::set`.

  ```cpp
  std::list<T>::iterator it;
  
  v.begin();      // Iterator to the first element
  v.end();        // Iterator past the last element
  ```

  ```cpp
  std::list<T>::reverse_iterator rit;
  
  v.rbegin();     // Reverse iterator to the last element
  v.rend();       // Reverse iterator past the first element
  ```

  ```cpp
  std::list<T>::const_iterator cit;
  
  v.cbegin();     // Const iterator to the first element
  v.cend();       // Const iterator past the last element
  ```

  ```cpp
  std::list<T>::const_reverse_iterator crit;
  
  v.crbegin();    // Const reverse iterator to the last element
  v.crend();      // Const reverse iterator past the first element
  ```

* **Time Complexities**:

  | Operation           | Time |
  | ------------------- | ---- |
  | Insert/Delete (pos) | O(1) |
  | Access (index)      | O(n) |



## Deque (`deque`)

* **Definition**: Double-ended queue with fast access and insert/delete at both ends.

* **Use Case**: When you need a queue with efficient operations on both ends.

* **Underlying Structure**: Array of blocks (bucketed array)

* **Declaration**:

  ```cpp
  #include <deque>
  
  std::deque<int> dq;
  ```

* **Key Operations**:

  ```cpp
  deque<int> dq;                // Create an empty deque
  dq.push_back(x);              // Add element at the back
  dq.push_front(x);             // Add element at the front
  dq.pop_back();                // Remove last element
  dq.pop_front();               // Remove first element
  dq[i];                        // Access i-th element (no bounds check)
  dq.at(i);                     // Access i-th element with bounds check
  dq.front();                   // First element
  dq.back();                    // Last element
  dq.insert(it, x);             // Insert x before iterator it
  dq.erase(it);                 // Erase element at iterator it
  dq.begin(), dq.end();         // Iterators for traversal
  dq.size();                    // Number of elements
  dq.empty();                   // Check if deque is empty
  dq.clear();                   // Remove all elements
  ```

* **Iterators**:

  `std::deque` provides full iterator support, including random access.

  ```cpp
  std::deque<T>::iterator it;
  
  v.begin();      // Iterator to the first element
  v.end();        // Iterator past the last element
  ```

  ```cpp
  std::deque<T>::reverse_iterator rit;
  
  v.rbegin();     // Reverse iterator to the last element
  v.rend();       // Reverse iterator past the first element
  ```

  ```cpp
  std::deque<T>::const_iterator cit;
  
  v.cbegin();     // Const iterator to the first element
  v.cend();       // Const iterator past the last element
  ```

  ```cpp
  std::deque<T>::const_reverse_iterator crit;
  
  v.crbegin();    // Const reverse iterator to the last element
  v.crend();      // Const reverse iterator past the first element
  ```

* **Time Complexities**:

  | Operation           | Time |
  | ------------------- | ---- |
  | Access (by index)   | O(1) |
  | push/pop front/back | O(1) |
  | insert/erase (mid)  | O(n) |



## Stack (`stack`)

* **Definition**: LIFO container with access only to top element.

* **Use Case**: Expression evaluation, backtracking, recursion.

* **Underlying Structure**: `deque` by default

* **Declaration**:

  ```cpp
  #include <stack>
  
  std::stack<int> s;
  ```

* **Key Operations**:

  ```cpp
  s.top();     // access top element
  s.push(x);   // push an element
  s.pop();     // remove top element
  s.empty();   // check if stack is empty
  s.size();    // get number of elements
  ```

* **Iterators**:

  `std::stack` container **does not provide iterators**.

  `std::stack` is an **adapter** container, meaning it provides a restricted interface over an underlying container (like `deque` or `vector`) to enforce **LIFO (Last In, First Out)** behavior. It deliberately hides access to all elements except the top.

* **Time Complexities**: All **O(1)**



## Queue (`queue`)

* **Definition**: FIFO container.

* **Use Case**: BFS, task scheduling.

* **Underlying Structure**: `deque` by default

* **Declaration**:

  ```cpp
  #include <queue>
  
  std::queue<int> q;
  ```

* **Key Operations**:

  ```cpp
  q.push(x);    // Add element at the back
  q.pop();      // Remove front element
  q.front();    // Access front element
  q.back();     // Access last element
  q.empty();    // Check if queue is empty
  q.size();     // Number of elements
  ```

* **Iterators**:

  `std::queue` **does not provide iterators**.

  Just like `std::stack`, the `std::queue` is a **container adapter**, designed to provide **FIFO (First In, First Out)** access. It deliberately hides the underlying container's elements to enforce this behavior.

* **Time Complexities**: All **O(1)**



## Priority Queue (`priority_queue`)

* **Definition**: Max-heap (largest at top)

* **Use Case**: Greedy problems, Dijkstra’s algorithm, job scheduling.

* **Underlying Structure**: Binary heap (using `vector`)

* **Declaration**:

  ```cpp
  #include <priority_queue>
  
  std::priority_queue<int> pq;
  ```

* **Key Operations**:

  ```cpp
  pq.push(x);		// Add element to the queue
  pq.pop();		// Remove the top (largest) element
  pq.top();		// Access the top (largest) element
  pq.empty();		// Check if the priority queue is empty
  pq.size();		// Get the number of elements in the queue
  ```

* **Iterators**:

  `std::priority_queue` **does not have iterators**.

  `std::priority_queue` is a container adapter designed to manage elements based on a priority order (usually a heap structure). It provides only the basic operations necessary for the priority queue, which are:

* **Min-heap**:

  ```cpp
  std::priority_queue<int, vector<int>, greater<int>> pq;
  ```

* **Time Complexities**:

  | Operation | Time     |
  | --------- | -------- |
  | push/pop  | O(log n) |
  | top       | O(1)     |



## Set (`set`)

* **Definition**: Ordered container with unique elements.

* **Use Case**: Fast search, removing duplicates, keeping sorted values.

* **Underlying Structure**: Red-Black Tree

* **Key Operations**:

  ```cpp
  s.insert(x);		// Add element to the set (no duplicates)
  s.erase(x);			// Remove element x from the set
  s.erase(it);		// Remove element at iterator it
  s.find(x);			// Find element x (returns iterator, or end() if not found)
  s.count(x);			// Check if element x exists (returns 0 or 1)
  s.empty();			// Check if the set is empty
  s.size();			// Number of elements in the set
  s.begin(), s.end();	// Iterators for traversal
  s.lower_bound(x);	// Return iterator to the first element not less than x
  s.upper_bound(x);	// Return iterator to the first element greater than x
  s.clear();			// Remove all elements
  ```

* **Iterators**:

  `std::set` provides bidirectional iterators, but NOT random access iterators like `vector` or `deque`.

  ```cpp
  std::set<T>::iterator it;
  
  v.begin();      // Iterator to the first element
  v.end();        // Iterator past the last element
  ```

  ```cpp
  std::set<T>::reverse_iterator rit;
  
  v.rbegin();     // Reverse iterator to the last element
  v.rend();       // Reverse iterator past the first element
  ```

  ```cpp
  std::set<T>::const_iterator cit;
  
  v.cbegin();     // Const iterator to the first element
  v.cend();       // Const iterator past the last element
  ```

  ```cpp
  std::set<T>::const_reverse_iterator crit;
  
  v.crbegin();    // Const reverse iterator to the last element
  v.crend();      // Const reverse iterator past the first element
  ```

  Since `std::set` maintains elements in a sorted order and enforces uniqueness, the values themselves are stored as `const`. This means:

  ```cpp
  *it = 10;  // Error: set iterators are read-only
  ```

* **Time Complexities**:

  | Operation        | Time     |
  | ---------------- | -------- |
  | insert/erase     | O(log n) |
  | find/lower/upper | O(log n) |



## Multiset (`multiset`)

* **Definition**: Like `set`, but allows duplicates.
* **Use Case**: Counting frequency of sorted values.
* **Underlying Structure**: Red-Black Tree
* **Time Complexities**: Same as `set`



## Unordered Set (`unordered_set`)

* **Definition**: Unique elements, not sorted.

* **Use Case**: Fast lookup where order doesn't matter.

* **Underlying Structure**: Hash Table

* **Time Complexities**:

  | Operation    | Avg Time | Worst Time |
  | ------------ | -------- | ---------- |
  | insert/erase | O(1)     | O(n)       |
  | find         | O(1)     | O(n)       |



## Map (`map`)

* **Definition**: Key-value store with unique, ordered keys.

* **Use Case**: Dictionary-type problems with sorted keys.

* **Underlying Structure**: Red-Black Tree

* **Key Operations**:

  ```cpp
  m[key] = value;
  m.insert({key, value});
  m.erase(key);
  m.find(key);
  ```

* **Time Complexities**: All **O(log n)**



## Multimap (`multimap`)

* **Definition**: Like `map`, but allows duplicate keys.
* **Use Case**: Grouping multiple values under the same key.
* **Note**: `m[key]` is **not** allowed.
* **Time Complexities**: Same as `map` — **O(log n)**



## Unordered Map (`unordered_map`)

* **Definition**: Key-value store with no ordering, fast average access.

* **Use Case**: Fast lookup table.

* **Underlying Structure**: Hash Table

* **Time Complexities**: 

  | Operation    | Avg Time | Worst Time |
  | ------------ | -------- | ---------- |
  | insert/erase | O(1)     | O(n)       |
  | access/find  | O(1)     | O(n)       |
