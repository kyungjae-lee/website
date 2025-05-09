[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Queues

# Queues



## Introduction

A **queue** is a linear data structure that follows the **First In, First Out (FIFO)** principle. Elements are added at the **back (enqueue)** and removed from the **front (dequeue)**. It is commonly used in scheduling, buffering, and breadth-first traversal scenarios.



<img src="./img/queue-using-singly-linked-list.png" alt="queue-using-singly-linked-list" width="700">



### Pros:

* **Simple FIFO Logic**: Maintains processing order, ideal for task scheduling and buffering.
* **Efficient Operations**: `O(1)` time for `enqueue` and `dequeue` (with a linked list or circular buffer).
* **Flexible Implementations**: Can be implemented using arrays, linked lists, or circular buffers.
* **Dynamic Size (Linked List)**: Grows as needed without reallocation.

### Cons:

* **Limited Access**: Only the front and rear elements can be accessed — no random access or indexed lookup.
* **Fixed Capacity (Array-Based)**: Unless dynamically resized, array-based queues have limited space.
* **Inefficient Deletion/Search**: Not suitable for arbitrary removal or searching through the queue.

### Compared to Stacks:

* **Better**: When processing order must be preserved (e.g., printers, CPU scheduling).
* **Worse**: When backtracking or nested operations are needed (stack excels there).



## Implementation (C++)

### Header (`queue.hpp`)

```cpp
#ifndef QUEUE_HPP
#define QUEUE_HPP

class node
{
public:
	node(int val) : data(val), p_next(nullptr) {}

	int data;
	node *p_next;
};

class queue
{
public:
	queue();
	~queue();
	void push(const int val);	// enqueue
	void pop();					// dequeue
	int front() const;
	int back() const;
	bool empty() const;
	int size() const;
	void clear();

private:
	node *p_front;
	node *p_back;
	int cnt;
};

#endif
```

### Source (`queue.cpp`)

```cpp
#include "queue.hpp"
#include <stdexcept>

queue::queue()
	: p_front(nullptr), p_back(nullptr), cnt(0)
{
	// do nothing
}

queue::~queue()
{
	while (!empty())
	{
		pop();
	}
}

void queue::push(const int val)
{
	node *p_new = new node(val);

	if (empty())
	{
		p_front = p_back = p_new;
	}
	else
	{
		p_back->p_next = p_new;
		p_back = p_new;
	}

	++cnt;
}

void queue::pop()
{
	if (empty())
	{
		throw std::runtime_error("Queue underflow");
	}

	node *p_del = p_front;
	p_front = p_front->p_next;
	delete p_del;

	if (nullptr == p_front)
	{
		p_back = nullptr;
	}

	--cnt;
}

int queue::front() const
{
	if (empty())
	{
		throw std::runtime_error("Queue is empty");
	}

	return p_front->data;
}

int queue::back() const
{
	if (empty())
	{
		throw std::runtime_error("Queue is empty");
	}

	return p_back->data;
}

bool queue::empty() const
{
	return 0 == cnt; // return nullptr == p_front;
}

int queue::size() const
{
	return cnt;
}

void queue::clear()
{
	while (p_front)
	{
		node *p_del = p_front;
		p_front = p_front->p_next;
		delete p_del;

		// or simply just call 'pop()' in this while loop
	}

	cnt = 0;
}
```

### Test Driver

```cpp
#include <iostream>
#include "queue.hpp"

int main(int argc, char *argv[])
{
    queue q;

    q.push(1);
    q.push(2);
    q.push(3);
    q.push(4);
    q.push(5);	// 1 -> 2 -> 3 -> 4 -> 5

    std::cout << q.front() << std::endl;	// 1
    std::cout << q.back() << std::endl;		// 5
	
	q.pop();
    std::cout << q.size() << std::endl;		// 4
    std::cout << q.empty() << std::endl;	// 0

    q.clear();
    std::cout << q.size() << std::endl;		// 0

    return 0;
}
```

```plain
1
5
4
0
0
```

