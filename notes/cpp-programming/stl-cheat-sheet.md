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

  | Operation   | Time Complexity | Description               |
  | ----------- | --------------- | ------------------------- |
  | `p.first;`  | O(1)            | Access the first element  |
  | `p.second;` | O(1)            | Access the second element |

* **Iterators**:

  `std::pair` **does not have an iterator**.

  `std::pair<T1, T2>` is a **simple struct-like container** that holds exactly **two values**: `first` and `second`. It’s not a sequence container like `vector`, `deque`, or `list`, so iteration doesn’t apply.



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

  | Operation             | Time Complexity | Description                                             |
  | --------------------- | --------------- | ------------------------------------------------------- |
  | `v.push_back(x);`     | O(1) amortized  | Add element at the back                                 |
  | `v.pop_back();`       | O(1)            | Remove the last element                                 |
  | `v[i];`               | O(1)            | Access i-th element (no bounds check)                   |
  | `v.at(i);`            | O(1)            | Access i-th element with bounds check                   |
  | `v.front();`          | O(1)            | Access first element                                    |
  | `v.back();`           | O(1)            | Access last element                                     |
  | `v.insert(it, x);`    | O(n)            | Insert element before iterator `it`                     |
  | `v.erase(it);`        | O(n)            | Erase element at iterator `it`                          |
  | `v.begin(), v.end();` | O(n)            | Iterators for traversal                                 |
  | `v.size();`           | O(1)            | Get the number of elements                              |
  | `v.empty();`          | O(1)            | Check if the vector is empty                            |
  | `v.clear();`          | O(n)            | Remove all elements                                     |
  | `v.resize(n);`        | O(n)            | Resize the vector to `n` elements                       |
  | `v.reserve(n);`       | O(n)            | Reserve space for `n` elements (prevents reallocations) |

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

  | Operation               | Time Complexity | Description                         |
  | ----------------------- | --------------- | ----------------------------------- |
  | `ls.push_back(x);`      | O(1)            | Add element at the back             |
  | `ls.push_front(x);`     | O(1)            | Add element at the front            |
  | `ls.pop_back();`        | O(1)            | Remove the last element             |
  | `ls.pop_front();`       | O(1)            | Remove the first element            |
  | `ls.insert(it, x);`     | O(1)            | Insert element before iterator `it` |
  | `ls.erase(it);`         | O(1)            | Erase element at iterator `it`      |
  | `ls.begin(), ls.end();` | O(n)            | Iterators for traversal             |
  | `ls.size();`            | O(n)            | Get the number of elements          |
  | `ls.empty();`           | O(1)            | Check if the list is empty          |
  | `ls.clear();`           | O(n)            | Remove all elements                 |
  | `ls.sort();`            | O(n log n)      | Sort the list                       |
  | `ls.reverse();`         | O(n)            | Reverse the list                    |

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

  | Operation               | Time Complexity | Description                           |
  | ----------------------- | --------------- | ------------------------------------- |
  | `dq.push_back(x);`      | O(1)            | Add element at the back               |
  | `dq.push_front(x);`     | O(1)            | Add element at the front              |
  | `dq.pop_back();`        | O(1)            | Remove the last element               |
  | `dq.pop_front();`       | O(1)            | Remove the first element              |
  | `dq[i];`                | O(1)            | Access i-th element (no bounds check) |
  | `dq.at(i);`             | O(1)            | Access i-th element with bounds check |
  | `dq.front();`           | O(1)            | Access the first element              |
  | `dq.back();`            | O(1)            | Access the last element               |
  | `dq.insert(it, x);`     | O(n)            | Insert element before iterator `it`   |
  | `dq.erase(it);`         | O(n)            | Erase element at iterator `it`        |
  | `dq.begin(), dq.end();` | O(n)            | Iterators for traversal               |
  | `dq.size();`            | O(1)            | Get the number of elements            |
  | `dq.empty();`           | O(1)            | Check if the deque is empty           |
  | `dq.clear();`           | O(n)            | Remove all elements                   |

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

  | Operation    | Time Complexity | Description                    |
  | ------------ | --------------- | ------------------------------ |
  | `s.top();`   | O(1)            | Access the top element         |
  | `s.push(x);` | O(1)            | Push an element onto the stack |
  | `s.pop();`   | O(1)            | Remove the top element         |
  | `s.empty();` | O(1)            | Check if the stack is empty    |
  | `s.size();`  | O(1)            | Get the number of elements     |

* **Iterators**:

  `std::stack` container **does not provide iterators**.

  `std::stack` is an **adapter** container, meaning it provides a restricted interface over an underlying container (like `deque` or `vector`) to enforce **LIFO (Last In, First Out)** behavior. It deliberately hides access to all elements except the top.



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

  | Operation    | Time Complexity | Description                             |
  | ------------ | --------------- | --------------------------------------- |
  | `q.push(x);` | O(1)            | Add element at the back                 |
  | `q.pop();`   | O(1)            | Remove the front element                |
  | `q.front();` | O(1)            | Access the front element                |
  | `q.back();`  | O(1)            | Access the last element                 |
  | `q.empty();` | O(1)            | Check if the queue is empty             |
  | `q.size();`  | O(1)            | Get the number of elements in the queue |

* **Iterators**:

  `std::queue` **does not provide iterators**.

  Just like `std::stack`, the `std::queue` is a **container adapter**, designed to provide **FIFO (First In, First Out)** access. It deliberately hides the underlying container's elements to enforce this behavior.



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

  | Operation     | Time Complexity | Description                             |
  | ------------- | --------------- | --------------------------------------- |
  | `pq.push(x);` | O(log n)        | Add element to the queue                |
  | `pq.pop();`   | O(log n)        | Remove the top (largest) element        |
  | `pq.top();`   | O(1)            | Access the top (largest) element        |
  | `pq.empty();` | O(1)            | Check if the priority queue is empty    |
  | `pq.size();`  | O(1)            | Get the number of elements in the queue |

* **Iterators**:

  `std::priority_queue` **does not have iterators**.

  `std::priority_queue` is a container adapter designed to manage elements based on a priority order (usually a heap structure). It provides only the basic operations necessary for the priority queue, which are:

* **Min-heap**:

  ```cpp
  std::priority_queue<int, vector<int>, greater<int>> pq;
  ```



## Set (`set`)

* **Definition**: Ordered container with unique elements.

* **Use Case**: Fast search, removing duplicates, keeping sorted values.

* **Underlying Structure**: Red-Black Tree

* **Declaration**:

  ```cpp
  #include <set>
  
  std::set<int> s;
  ```

* **Key Operations**:

  | Operation           | Time Complexity | Description                                              |
  | ------------------- | --------------- | -------------------------------------------------------- |
  | `s.insert(x);`      | O(log n)        | Add element to the set (no duplicates)                   |
  | `s.erase(x);`       | O(log n)        | Remove element x from the set                            |
  | `s.erase(it);`      | O(log n)        | Remove element at iterator it                            |
  | `s.find(x);`        | O(log n)        | Find element x (returns iterator, or end() if not found) |
  | `s.count(x);`       | O(log n)        | Check if element x exists (returns 0 or 1)               |
  | `s.empty();`        | O(1)            | Check if the set is empty                                |
  | `s.size();`         | O(1)            | Number of elements in the set                            |
  | `s.lower_bound(x);` | O(log n)        | Return iterator to the first element not less than x     |
  | `s.upper_bound(x);` | O(log n)        | Return iterator to the first element greater than x      |
  | `s.clear();`        | O(n)            | Remove all elements                                      |

* **Iterators**:

  `std::set` provides bidirectional iterators, but NOT random access iterators like `vector` or `deque`.

  ```cpp
  std::set<T>::iterator it;
  
  s.begin();      // Iterator to the first element
  s.end();        // Iterator past the last element
  ```

  ```cpp
  std::set<T>::reverse_iterator rit;
  
  s.rbegin();     // Reverse iterator to the last element
  s.rend();       // Reverse iterator past the first element
  ```

  ```cpp
  std::set<T>::const_iterator cit;
  
  s.cbegin();     // Const iterator to the first element
  s.cend();       // Const iterator past the last element
  ```

  ```cpp
  std::set<T>::const_reverse_iterator crit;
  
  s.crbegin();    // Const reverse iterator to the last element
  s.crend();      // Const reverse iterator past the first element
  ```

  Since `std::set` maintains elements in a sorted order and enforces uniqueness, the values themselves are stored as `const`. This means:

  ```cpp
  *it = 10;  // Error: set iterators are read-only
  ```



## Multiset (`multiset`)

* **Definition**: Like `set`, but allows duplicates.

* **Use Case**: Counting frequency of sorted values.

* **Underlying Structure**: Red-Black Tree

* **Declaration**:

  ```cpp
  #include <multiset>
  
  std::multiset<int> ms;
  ```

* **Key Operations**:

  | Operation            | Time Complexity | Description                           |
  | -------------------- | --------------- | ------------------------------------- |
  | `ms.insert(x);`      | O(log n)        | Insert element x (duplicates allowed) |
  | `ms.erase(x);`       | O(log n)        | Erase all instances of x              |
  | `ms.erase(it);`      | O(log n)        | Erase element at iterator it          |
  | `ms.find(x);`        | O(log n)        | Returns iterator to one instance of x |
  | `ms.count(x);`       | O(log n)        | Returns number of elements equal to x |
  | `ms.size();`         | O(1)            | Number of elements                    |
  | `ms.empty();`        | O(1)            | Check if set is empty                 |
  | `ms.clear();`        | O(n)            | Remove all elements                   |
  | `ms.lower_bound(x);` | O(log n)        | Iterator to first element >= x        |
  | `ms.upper_bound(x);` | O(log n)        | Iterator to first element > x         |
  | `ms.equal_range(x);` | O(log n)        | Pair of iterators with range of x     |

* **Iterators**: `std::multiset` provides full iterator support, just like `set`. Iterators are **bidirectional** (you can do `++it` and `--it`, but not `it + n`).

  ```cpp
  std::multiset<T>::iterator it;
  
  ms.begin();      // Iterator to the first element
  ms.end();        // Iterator past the last element
  ```

  ```cpp
  std::multiset<T>::reverse_iterator rit;
  
  ms.rbegin();     // Reverse iterator to the last element
  ms.rend();       // Reverse iterator past the first element
  ```

  ```cpp
  std::multiset<T>::const_iterator cit;
  
  ms.cbegin();     // Const iterator to the first element
  ms.cend();       // Const iterator past the last element
  ```

  ```cpp
  std::set<T>::const_reverse_iterator crit;
  
  s.crbegin();    // Const reverse iterator to the last element
  s.crend();      // Const reverse iterator past the first element
  ```

  

## Unordered Set (`unordered_set`)

* **Definition**: Unique elements, not sorted.

* **Use Case**: Fast lookup where order doesn't matter.

* **Underlying Structure**: Hash Table

* **Declaration**:

  ```cpp
  #include <unordered_set>
  
  std::unordered_set<int> us;
  ```

* **Key Operations**:

  | Operation       | Time Complexity                | Description                                                |
  | --------------- | ------------------------------ | ---------------------------------------------------------- |
  | `us.insert(x);` | O(1) average, <br />O(n) worst | Insert element x                                           |
  | `us.erase(x);`  | O(1) average, <br />O(n) worst | Erase element x                                            |
  | `us.find(x);`   | O(1) average, <br />O(n) worst | Returns iterator to x, or `us.end()` if not found          |
  | `us.count(x);`  | O(1) average,<br />O(n) worst  | Returns 1 if x exists, 0 otherwise (no duplicates allowed) |
  | `us.size();`    | O(1)                           | Number of elements                                         |
  | `us.empty();`   | O(1)                           | Check if set is empty                                      |
  | `us.clear();`   | O(n)                           | Remove all elements                                        |

* **Iterators**: `std::unordered_set` provides iterators, but they are not ordered and not random-access.

  ```cpp
  std::unordered_set<K, V>::iterator it;
  
  us.begin();       // Iterator to the first element (in hash bucket order)
  us.end();         // Iterator past the last element
  ```

  ```cpp
  std::unordered_set<K, V>::const_iterator cit;
  
  us.cbegin();      // Const iterator to the first element
  us.cend();        // Const iterator past the last element
  ```

  > `unordered_set` does **not** provide `.rbegin()` or `.rend()` because the order is not defined. Only forward iterators! Iteration order is not predictable.
  >
  > Cannot modify the element value through the iterator.



## Map (`map`)

* **Definition**: Key-value store with unique, ordered keys.

* **Use Case**: Dictionary-type problems with sorted keys.

* **Underlying Structure**: Red-Black Tree

* **Declaration**:

  ```cpp
  #include <map>
  
  std::map<int, int> m;
  ```

* **Key Operations**:

  | Operation           | Time Complexity | Description                                  |
  | ------------------- | --------------- | -------------------------------------------- |
  | `m[key] = val;`     | O(log n)        | Insert or update value at key                |
  | `m.insert({k, v});` | O(log n)        | Insert pair (doesn't overwrite existing key) |
  | `m.erase(k);`       | O(log n)        | Erase element by key                         |
  | `m.erase(it);`      | O(log n)        | Erase element by iterator                    |
  | `m.find(k);`        | O(log n)        | Returns iterator to element with key k       |
  | `m.count(k);`       | O(log n)        | Returns 1 if key exists, else 0              |
  | `m.size();`         | O(1)            | Number of elements                           |
  | `m.empty();`        | O(1)            | Check if map is empty                        |
  | `m.clear();`        | O(n)            | Remove all elements                          |
  | `m.lower_bound(k);` | O(log n)        | Iterator to first key >= k                   |
  | `m.upper_bound(k);` | O(log n)        | Iterator to first key > k                    |
  | `m.equal_range(k);` | O(log n)        | Pair of iterators (lower_bound, upper_bound) |

* **Iterators**:  `std::map` provides full iterator support.

  ```cpp
  std::map<K, V>::iterator it;
  
  ms.begin();      // Iterator to the first (smallest key) element
  ms.end();        // Iterator past the last element
  ```

  ```cpp
  std::map<K, V>::reverse_iterator rit;
  
  ms.rbegin();     // Reverse iterator to the last element
  ms.rend();       // Reverse iterator past the first element
  ```

  ```cpp
  std::map<K, V>::const_iterator cit;
  
  ms.cbegin();     // Const iterator to the first element
  ms.cend();       // Const iterator past the last element
  ```

  ```cpp
  std::map<K, V>::const_reverse_iterator crit;
  
  ms.crbegin();    // Const reverse iterator to the last element
  ms.crend();      // Const reverse iterator past the first element
  ```



## Multimap (`multimap`)

* **Definition**: Like `map` ordered, but allows duplicate keys.

* **Use Case**: Grouping multiple values under the same key.

* **Note**: `m[key]` is **not** allowed.

* **Underlying Structure**: Red-Black Tree

* **Declaration**:

  ```cpp
  #include <multimap>
  
  std::multimap<int, int> m;
  ```

* **Key Operations**:

  | Operation            | Time Complexity | Description                                             |
  | -------------------- | --------------- | ------------------------------------------------------- |
  | `mm.insert({k, v});` | O(log n)        | Insert key-value pair (duplicates allowed)              |
  | `mm.erase(k);`       | O(log n + m)    | Erase all elements with key `k`                         |
  | `mm.erase(it);`      | O(log n)        | Erase element at iterator `it`                          |
  | `mm.find(k);`        | O(log n)        | Find element by key `k` (returns iterator)              |
  | `mm.count(k);`       | O(log n)        | Count occurrences of key `k`                            |
  | `mmmsize();`         | O(1)            | Number of elements in the multimap                      |
  | `mm.empty();`        | O(1)            | Check if the multimap is empty                          |
  | `mm.clear();`        | O(n)            | Remove all elements from the multimap                   |
  | `mm.lower_bound(k);` | O(log n)        | Iterator to first element with key >= `k`               |
  | `mm.upper_bound(k);` | O(log n)        | Iterator to first element with key > `k`                |
  | `mm.equal_range(k);` | O(log n)        | Pair of iterators to the range of elements with key `k` |

* **Iterators**:  `std::multimap` provides full iterator support.

  ```cpp
  std::multimap<K, V>::iterator it;
  
  mm.begin();      // Iterator to the first (smallest key) element
  mm.end();        // Iterator past the last element
  ```

  ```cpp
  std::multimap<K, VT>::reverse_iterator rit;
  
  mm.rbegin();     // Reverse iterator to the last element
  mm.rend();       // Reverse iterator past the first element
  ```

  ```cpp
  std::multimap<K, V>::const_iterator cit;
  
  mm.cbegin();     // Const iterator to the first element
  mm.cend();       // Const iterator past the last element
  ```

  ```cpp
  std::multimap<K, V>::const_reverse_iterator crit;
  
  mm.crbegin();    // Const reverse iterator to the last element
  mm.crend();      // Const reverse iterator past the first element
  ```



## Unordered Map (`unordered_map`)

* **Definition**: Key-value store with no ordering, fast average access.

* **Use Case**: Fast lookup table.

* **Underlying Structure**: Hash Table

* **Declaration**:

  ```cpp
  #include <unordered_map>
  
  std::unordered_map<int, int> m;
  ```

* **Key Operations**:

  | Operation            | Time Complexity            | Description                                             |
  | -------------------- | -------------------------- | ------------------------------------------------------- |
  | `um[key] = val;`     | O(1) avg, <br />O(n) worst | Insert or update value at key                           |
  | `um.insert({k, v});` | O(1) avg, <br />O(n) worst | Insert key-value pair (doesn't overwrite existing key)  |
  | `um.erase(k);`       | O(1) avg, <br />O(n) worst | Erase element by key                                    |
  | `um.erase(it);`      | O(1) avg                   | Erase element at iterator `it`                          |
  | `um.find(k);`        | O(1) avg, <br />O(n) worst | Find element by key `k` (returns iterator)              |
  | `um.count(k);`       | O(1) avg, <br />O(n) worst | Returns 1 if key exists, else 0                         |
  | `um.size();`         | O(1)                       | Number of elements in the unordered map                 |
  | `um.empty();`        | O(1)                       | Check if the unordered map is empty                     |
  | `um.clear();`        | O(n)                       | Remove all elements from the unordered map              |
  | `um.lower_bound(k);` | O(1) avg, <br />O(n) worst | Iterator to element with key >= `k`                     |
  | `um.upper_bound(k);` | O(1) avg, <br />O(n) worst | Iterator to element with key > `k`                      |
  | `um.equal_range(k);` | O(1) avg, <br />O(n) worst | Pair of iterators to the range of elements with key `k` |

* **Iterators**:  `std::unordered_map` provides full iterator support.

  ```cpp
  std::unordered_map<K, V>::iterator it;
  
  um.begin();      // Iterator to the first element
  um.end();        // Iterator past the last element
  ```

  ```cpp
  std::unordered_map<K, V>::reverse_iterator rit;
  
  um.rbegin();     // Reverse iterator to the last element
  um.rend();       // Reverse iterator past the first element
  ```

  ```cpp
  std::unordered_map<K, V>::const_iterator cit;
  
  um.cbegin();     // Const iterator to the first element
  um.cend();       // Const iterator past the last element
  ```

  ```cpp
  std::unordered_map<K, V>::const_reverse_iterator crit;
  
  um.crbegin();    // Const reverse iterator to the last element
  um.crend();      // Const reverse iterator past the first element
  ```

  `unordered_map` **does provide `rbegin()` and `rend()`** iterators. 

  While the order of elements in an `unordered_map` is not predictable (because they are stored based on hash values), the reverse iterators (`rbegin()` and `rend()`) allow you to iterate through the elements in reverse order of the buckets' internal arrangement. However, this reverse iteration will still follow the same unpredictable order, based on how the elements are distributed across buckets.
