[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Introduction to Containers

# Introduction to Containers



## Traditional Containers

Containers are used to store collections of homogeneous data. A C-style array is a common example.

```c
int numbers[100];
```

Arrays are the original and most widely known type of container. They allow you to store a sequence of elements of the same type. However, arrays come with several major limitations:

- Accessing an out-of-bounds index results in undefined behavior.
- They have a fixed size and cannot grow or shrink dynamically.
- Their size must be known and defined at compile time.



## STL Containers

The STL builds on the concept of data sequences by offering a variety of higher-level containers, each with distinct features. Some containers are dynamically sized, some maintain order, while others are optimized for fast insertions or efficient searching.Three types of containers:

* Sequence containers
  - Elements are accessed by their position (index) in a linear sequence.
* Associative containers
  - Elements are accessed by a unique key, allowing for efficient lookup.
* Container adapters
  - These are not standalone containers but provide a different interface by wrapping existing containers (e.g., `stack`, `queue`).

### Sequence Containers

Sequence containers maintain a strict linear order and allow access to elements by their position (index) within the sequence. Some sequence containers guarantee contiguous memory allocation, such as:

- `array`
- `vector`

Others, such as linked list types, do not provide contiguous storage.

### Associative Containers

Elements in associative containers are accessed via keys rather than by their position in a sequence. These containers typically do not maintain a strict order and are optimized for efficient searching rather than human-readable ordering. Common associative containers include:

- `map`
- `set`
- `unordered_map`
- `unordered_set`

### Adapter Containers

Adapter containers are wrappers around existing containers that provide a different interface.
 They are not standalone container implementations but instead reuse existing containers (such as `deque` or `vector`) under the hood while exposing a modified API.

Common adapter containers include:

- `stack`
- `queue`
- `priority_queue`

For example,

```cpp
template <class T, class Container = deqeu<T>> class stack;

template <class T, class Container = deque<T>> class queue;

template <class T, class Container = vector<T>,
	class Compare = less<typename Container::value_type>>
    class primary_queue;
```



## What Can Containers Do?

Each container provides a set of member functions that make them easier to work with than plain old arrays. Additionally, the STL offers a wide range of powerful free functions in the `<algorithm>` and `<functional>` headers that can operate on containers. All containers—except for the adapters—support iterators, which allow element access in a uniform and flexible way.

### Example - Accessor Methods

| Container (`std::`) | operator[] |  at  | front | back | data | top  |
| :-----------------: | :--------: | :--: | :---: | :--: | :--: | :--: |
|        array        |     X      |  X   |   X   |  X   |  X   |      |
|        deque        |     X      |  X   |   X   |  X   |      |      |
|    forward_list     |            |      |   X   |      |      |      |
|        list         |            |      |   X   |  X   |      |      |
|         map         |     X      |  X   |       |      |      |      |
|        queue        |            |      |   X   |  X   |      |      |
|         set         |            |      |       |      |      |      |
|        stack        |            |      |       |      |      |  X   |
|    unordered_map    |     X      |  X   |       |      |      |      |
|    unordered_set    |            |      |       |      |      |      |
|       vector        |     X      |  X   |   X   |  X   |  X   |      |

As shown above, the STL provides a consistent interface across different containers, but it is the programmer’s responsibility to choose the one that best fits the specific situation.
