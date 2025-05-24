[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Containers

# Containers



## Traditional Containers

Containers are used to store collections of homogeneous data. C-style array is a good example.

```c
int numbers[100];
```

Arrays are the original containers that are most widely known. Arrays allow you to store sequences of data of the same type. However, they have some major shortcomings:

* Undefined behavior if you try to access index outside the range.
* Fixed size: cannot grow or shrink.
* Size must be defined at compile time.



## STL Containers

The STL extends on the idea of sequences of data by providing  different kinds of higher-level containers that have different features. Some are dynamically sized, some are ordered, and some offer quick insertions and others offer quick searching.

Three types of containers:

* Sequence containers
  * Elements are accessed by index.
* Associative containers
  * Elements are accessed by key.
* Adapter containers
  * Not fully implemented containers, but simply a new API on top of an existing container.

### Sequence Containers

Sequence containers are ordered in a strict linear sequence, and are accessed via their position index within the sequence. Some sequences are guaranteed to be allocated in contiguous memory blocks:

* array
* vector

While others like linked list types do not.

### Associative Containers

Elements stored in associative containers are accessed via their keys, and not by their position in the sequence. Their order can be more loosely defined and usually optimized for searching algorithms rather than humans:

* maps
* sets
* Unordered_maps
* Unordered_sets

### Adapter Containers

Adapter containers are wrappers around existing containers that provide a new API:

* stack
* queue
* priority_queue

The adapter containers are not real containers in that there is not an actual implementation but instead there's a different API applied to an existing container.

For example,

```cpp
template <class T, class Container = deqeu<T>> class stack;
template <class T, class Container = deque<T>> class queue;
template <class T, class Container = vector<T>,
	class Compare = less<typename Container::value_type>>
    class primary_queue;
```
