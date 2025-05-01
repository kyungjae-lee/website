[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > STL Cheat Sheet

# STL Cheat Sheet



## Pair (`pair`)

* **Definition**: Combines two values into one object (can be different types).

* **Use Case**: Useful for returning multiple values, sorting by custom keys, or using as keys in maps.

* **Underlying Structure**: Simple struct

* **Key Operations**:

  ```cpp
  pair<int, int> p = {1, 2};
  p.first; p.second;
  ```

* **Time Complexity**: Access: **O(1)**



## Vector (`vector`)

* **Definition**: Dynamic array with contiguous storage.

* **Use Case**: Ideal for indexed access and resizable sequences.

* **Underlying Structure**: Dynamic array (heap-allocated)

* **Key Operations**:

  ```cpp
  v.push_back(x);     // add at end
  v.emplace_back(x);  // efficient add
  v.pop_back();       // remove from end
  v.insert(it, x);    // insert at position
  v.erase(it);        // erase at position
  v.clear();          // remove all elements
  v[i], v.at(i), v.back()
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

  ```cpp
  ls.push_back(x); ls.push_front(x);
  ls.pop_back(); ls.pop_front();
  ls.insert(it, x); ls.erase(it);
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

* **Key Operations**:

  ```cpp
  dq.push_front(x); dq.push_back(x);
  dq.pop_front(); dq.pop_back();
  dq[i]; dq.at(i);
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

* **Key Operations**:

  ```cpp
  s.top();     // access top element
  s.push(x);   // push an element
  s.pop();     // remove top element
  s.empty();   // check if stack is empty
  s.size();    // get number of elements
  ```

* **Time Complexities**: All **O(1)**



## Queue (`queue`)

* **Definition**: FIFO container.

* **Use Case**: BFS, task scheduling.

* **Underlying Structure**: `deque` by default

* **Key Operations**:

  ```cpp
  q.push(x); q.pop();
  q.front(); q.back();
  ```

* **Time Complexities**: All **O(1)**



## Priority Queue (`priority_queue`)

* **Definition**: Max-heap (largest at top)

* **Use Case**: Greedy problems, Dijkstra’s algorithm, job scheduling.

* **Underlying Structure**: Binary heap (using `vector`)

* **Key Operations**:

  ```cpp
  pq.push(x); pq.pop(); pq.top();
  ```

* **Min-heap**:

  ```cpp
  priority_queue<int, vector<int>, greater<int>> pq;
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
  s.insert(x); s.erase(x); s.find(x);
  s.lower_bound(x); s.upper_bound(x);
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
