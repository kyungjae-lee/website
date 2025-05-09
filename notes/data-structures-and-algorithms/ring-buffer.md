[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Ring Buffer

# Ring Buffer



## Introduction

A **ring buffer** is a fixed-size data structure that uses a single, contiguous block of memory as a circular queue. It maintains two indices (`front` and `back`) to manage reading and writing. When the end of the buffer is reached, it wraps around to the beginning — forming a logical circle.

### Pros:

* **Constant-Time Operations**: `O(1)` time for enqueue and dequeue (no shifting of elements).
* **Fixed Memory Footprint**: Efficient for systems with limited memory or real-time constraints.
* **Cache-Friendly**: Data is stored contiguously in memory.
* **Ideal for Streaming**: Commonly used in audio buffers, UART RX/TX buffers, and producer-consumer models.

### Cons:

* **Fixed Capacity**: Must predefine buffer size; resizing is non-trivial.
* **Wasted Slot (optional)**: Some implementations leave one slot empty to distinguish full vs. empty.
* **Requires Careful Index Management**: Logic for wraparound and full/empty detection can be error-prone.
* **Not Suited for Arbitrary Insertion/Deletion**: Only supports FIFO (queue-style) behavior.



## Implementation (C++)

### Header (`rbuffer.hpp`)

```cpp
#ifndef RBUFFER_HPP
#define RBUFFER_HPP

#include <iostream>

class rbuffer
{
public:
	rbuffer(const int cap);
	~rbuffer();
	bool empty() const;
	bool full() const;
	void push(const int val);
	int pop();
	int front() const;
	int size() const;
	int capacity() const;
	void clear();

private:
	int *buffer;
	int cap;
	int head;
	int tail;
	int sz;
};

#endif
```

### Source (`rbuffer.cpp`)

```cpp
#include "rbuffer.hpp"
#include <stdexcept>

rbuffer::rbuffer(const int cap)
	: cap(cap), head(0), tail(0), sz(0)
{
	buffer = new int[cap];
}

rbuffer::~rbuffer()
{
	delete[] buffer;
}

bool rbuffer::empty() const
{
	return sz == 0;
}

bool rbuffer::full() const
{
	return sz == cap;
}

void rbuffer::push(const int val)
{
	if (full())
	{
		throw std::overflow_error("Ring buffer overflow");
	}

	buffer[tail++] = val;
	tail %= cap;
	++sz;
}

int rbuffer::pop()
{
	if (empty())
	{
		throw std::underflow_error("Ring buffer underflow");
	}

	int val = buffer[head++];
	head %= cap;
	--sz;

	return val;
}

int rbuffer::front() const
{
	if (empty())
	{
		throw std::runtime_error("Ring buffer is empty");
	}

	return buffer[head];
}

int rbuffer::size() const
{
	return sz;
}

int rbuffer::capacity() const
{
	return cap;
}

void rbuffer::clear()
{
	head = tail = sz = 0;
}
```

### Test Driver

```cpp
5 0
0 3
0
1 5
0
5
1
0 3
3
```

