[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Container Adaptor - `std::queue`

# Container Adaptor - `std::queue`



## `std::queue`

* Declared in the `<queue>` header file.
* First-In, First-Out (FIFO) data structure.
* Implemented as an **adaptor** over other STL containers.
  * Can use `list`, or `deque` as the underlying container.
  * Queue methods work through delegation—they internally call the `front()`, `back()`, `push_back()`, and `pop_front()` functions of the underlying container.
    * Since `vector` does not support `pop_front()` due to inefficiency, it cannot be used as the underlying container for `queue`.
  
* Elements are pushed at the back and popped from the front.
* Iterators are **not supported**.
* Use cases:
  * OS schedulers
  * Inter-task/thread communication in multi-threaded systems
  * Breadth-First Search (BFS) in graphs
  * Real-time data streams / buffers in audio/video streaming, sensor data handling
  * Printer spoolers



### Initialization

Because `std::queue` is a container adaptor, you have the flexibility to specify the underlying container—such as `deque` or `list`—at the time of queue creation.

```cpp
std::queue<int> q1;						// deque (by default)
std::queue<int, std::list<int>> q2;		// list
std::queue<int, std::deque<int>> q3;	// deque
```

> Note that `std::queue<int, std::vector<int>> q` will fail to compile because `std::vector` lacks `pop_front()`.

### Queue Operations

For more information, see [cppreference.com](https://en.cppreference.com/w/).

| Operation | Behavior                                               |
| --------- | ------------------------------------------------------ |
| `push()`  | **Insert** an element at the **back** of the queue.    |
| `pop()`   | **Remove** an element from the **front** of the queue. |
| `front()` | Access the element at the front.                       |
| `back()`  | Access the element at the back.                        |
| `empty()` | Is the queue empty?                                    |
| `size()`  | Number of elements in the queue .                      |



## Project: Usage of `std::queue`

It's best to adhere to the fundamental operations of a queue. Introducing additional methods like `insert()` compromises the integrity of the queue's intended behavior.

```cpp
#include <iostream>
#include <queue>

// Prints the queue by repeatedly removing elements from the front.
// Note that this function is passed a queue 'by value' so whatever happens
// inside this function will not affect the original queue.
template <typename T>
void print(std::queue<T> q)
{
    std::cout << "[ ";
    while (!q.empty())
    {
        T elem = q.front();
        q.pop();
        std::cout << elem << " ";
    }
    std::cout << "]" << std::endl;
}

// push(), pop(), empty(), size()
void test(void)
{
    std::cout << "\nTEST" << std::endl;

    std::queue<int> q;

    for (int n : {1, 2, 3, 4, 5})
    {
        q.push(n);
    }
    print(q);

    std::cout << "Front: " << q.front() << std::endl;
    std::cout << "Back: " << q.back() << std::endl;

    q.push(100);
    print(q);

    q.pop();
    q.pop();
    print(q);

    while (!q.empty())
    {
        q.pop();
    }
    print(q);

    std::cout << "Size: " << q.size() << std::endl;

    q.push(10);
    q.push(100);
    q.push(1000);
    print(q);

    std::cout << "Front: " << q.front() << std::endl;
    std::cout << "Back: " << q.back() << std::endl;

    q.front() = 5;
    q.back() = 5000;

    print(q);
    std::cout << "Front: " << q.front() << std::endl;
    std::cout << "Back: " << q.back() << std::endl;
}

int main(int argc, char *argv[])
{
    test();
    return 0;
}
```

```plain

TEST
[ 1 2 3 4 5 ]
Front: 1
Back: 5
[ 1 2 3 4 5 100 ]
[ 3 4 5 100 ]
[ ]
Size: 0
[ 10 100 1000 ]
Front: 10
Back: 1000
[ 5 100 5000 ]
Front: 5
Back: 5000
```
