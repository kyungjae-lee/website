[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Container Adaptor - `std::priority_queue`

# Container Adaptor - `std::priority_queue`



## `std::priority_queue`

* Declared in the `<queue>` header file.
* `priority_queue` is a container adaptor, just like `stack` and `queue`.
* It allows insertion and removal of elements based on priority, always from the front of the container.
* Elements are inserted according to **priority** — by default, the largest value is always at the front.
  * When accessing the front element, it is guaranteed to be the largest in the container.

* Internally, elements are stored in a `vector` by default, but a **heap** data structure is used behind the scenes. 
  - (Note: A heap data structure is different from heap memory — don’t confuse the two.)
* Iterators are **not supported** because they don’t align with how a priority queue manages element order.
* Use cases:
  * OS priority-based task scheduling and process management
  * Event-driven simulations (e.g., network simulators, hardware simulators, CPU cycle modeling)
  * Huffman Coding (data compression algorithms; ZIP, JPEG, MP3)
  * Top-K / N largest or smallest elements (e.g., data analytics, leaderboard ranking, search engines)
  * Job/print queue with prioritization (e.g., print servers, ticketing systems)
  * Bandwidth management in networking (e.g., routers, firewalls, packet shapers)


### Initialization

Because `std::queue` is a container adaptor, you have the flexibility to specify the underlying container—such as `deque` — at the time of priority queue creation.

```cpp
std::priority_queue<int> q1;	// vector (by default)
std::priority_queue<int, std::deque<int>, std::less<int>> q2;
```

> L2: `std::less<int>` makes it a max-heap.

### Priority Queue Operations

For more information, see [cppreference.com](https://en.cppreference.com/w/).

| Operation | Behavior                                                     |
| --------- | ------------------------------------------------------------ |
| `push()`  | Insert an element in sorted order.                           |
| `pop()`   | Remove the top element (highest priority; by default greatest). |
| `top()`   | Access the top element (highest priority; by default greatest). |
| `empty()` | Is the priority queue empty?                                 |
| `size()`  | Number of elements in the priority queue .                   |

```cpp
std::priority_queue<int> pq;	// vector

pq.push(10);
pq.push(20);
pq.push(3);
pq.push(4);

std::cout << pq.top();	// 20 (largest)
pq.pop();				// remove 20
pq.top();				// 10 (largest)
```



## Project: Usage of `std::priority_queue`

```cpp
#include <iostream>
#include <queue>

class person
{
public:
    person() : name{"Unkown"}, age{0} {}
    person(std::string name, int age)
        : name{name}, age{age}
    {}
    bool operator<(const person &rhs) const
    {
        return this->age < rhs.age;
    }
    bool operator==(const person &rhs) const 
    {
        return (this->name == rhs.name && this->age == rhs.age);
    }
    
private:
    friend std::ostream& operator<<(std::ostream &os, const person &p);
    std::string name;
    int age;
};

// Overloading the insertion operator as a global function.
std::ostream& operator<<(std::ostream &os, const person &p)
{
    os << p.name << ":" << p.age;
    return os;
}

// Prints the priority queue by repeatedly topping and popping the elements.
// Note that this function is passed a priority queue 'by value' so whatever
// happens inside this function will not affect the original priority_queue.
template <typename T>
void print(std::priority_queue<T> pq)
{
    std::cout << "[ ";
    while (!pq.empty())
    {
        T elem = pq.top();
        pq.pop();
        std::cout << elem << " ";
    }
    std::cout << "]" << std::endl;
}

// push(), size(), top(), pop()
void test1(void)
{
    std::cout << "\nTEST1" << std::endl;

    std::priority_queue<int> pq;
    for (int n : {3, 5, 7, 12, 23, 12, 4, 100, 0, 3, 5, 7})
    {
        pq.push(n);
    }

    std::cout << "Size: " << pq.size() << std::endl;
    std::cout << "Top: " << pq.top() << std::endl;

    print(pq);

    pq.pop();
    print(pq);
}

// push()
void test2(void)
{
    std::cout << "\nTEST2" << std::endl;

    std::priority_queue<person> pq;

    // Ordered by age (based on the overloaded 'operator<')
    pq.push(person{"A", 10});
    pq.push(person{"B", 1});
    pq.push(person{"C", 14});
    pq.push(person{"D", 18});
    pq.push(person{"E", 17});
    pq.push(person{"F", 27});
    print(pq);
}

int main(int argc, char *argv[])
{
    test1();
    test2();
    return 0;
}
```

```plain

TEST1
Size: 12
Top: 100
[ 100 23 12 12 7 7 5 5 4 3 3 0 ]
[ 23 12 12 7 7 5 5 4 3 3 0 ]

TEST2
[ F:27 D:18 E:17 C:14 A:10 B:1 ]
```
