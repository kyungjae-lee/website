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



## Implementation (C)

### Header (`rbuffer.h`)

```c
#ifndef RBUFFER_H
#define RBUFFER_H

#include <stdbool.h>

typedef struct
{
	int *buffer;
	int capacity;
	int head;
	int tail;
	int size;
} rbuffer;

// initializes the ring buffer
bool rbuffer_init(rbuffer *rb, const int cap);

// frees allocated memory
void rbuffer_destroy(rbuffer *rb);

// adds an element to the buffer
bool rbuffer_push(rbuffer *rb, const int val);

// removes the oldest element
bool rbuffer_pop(rbuffer *rb);

// finds the oldest element if any
bool rbuffer_front(rbuffer *rb, int *out_val);

// checks if buffer is empty
bool rbuffer_empty(const rbuffer *rb);

// checks if buffer is full
bool rbuffer_full(const rbuffer *rb);

// clears the buffer
void rbuffer_clear(rbuffer *rb);

// returns the number of elements currently in the buffer
int rbuffer_size(const rbuffer *rb);

#endif
```

### Source (`rbuffer.c`)

```c
#include "rbuffer.h"
#include <stdlib.h>
#include <stddef.h>

// initializes the ring buffer
bool rbuffer_init(rbuffer *rb, const int cap)
{
	if (!rb || cap <= 0)
	{
		return false;
	}

	rb->buffer = (int *)malloc(cap * sizeof(int));
	if (!rb->buffer)
	{
		// memory allocation failed
		return false;
	}

	rb->head = 0;
	rb->tail = 0;
	rb->size = 0;
	rb->capacity = cap;

	return true;
}

// frees allocated memory
void rbuffer_destroy(rbuffer *rb)
{
	if (!rb)
	{
		return;
	}

	if (rb->buffer)
	{
		free(rb->buffer);
		rb->buffer = NULL;
	}

	rb->head = 0;
	rb->tail = 0;
	rb->size = 0;
	rb->capacity = 0;
}

// adds an element to the buffer
bool rbuffer_push(rbuffer *rb, const int val)
{
	if (!rb || !rb->buffer || rbuffer_full(rb))
	{
		return false;
	}

	rb->buffer[rb->tail] = val;
	rb->tail = (rb->tail + 1) % rb->capacity;
	rb->size++;

	return true;
}

// removes the oldest element
bool rbuffer_pop(rbuffer *rb)
{
	if (!rb || !rb->buffer || rbuffer_empty(rb))
	{
		return false;
	}

	rb->head = (rb->head + 1) % rb->capacity;
	rb->size--;

	return true;
}

// finds the oldest element if any
bool rbuffer_front(rbuffer *rb, int *out_val)
{
	if (!rb || !rb->buffer || rbuffer_empty(rb))
	{
		return false;
	}
	
	if (out_val)
	{
		*out_val = rb->buffer[rb->head];
	}

	return true;
}

// checks if buffer is empty
bool rbuffer_empty(const rbuffer *rb)
{
	return !rb || (rb->size == 0);
}

// checks if buffer is full
bool rbuffer_full(const rbuffer *rb)
{
	return !rb && rb->buffer && (rb->size == rb->capacity);
}

// clears the buffer
void rbuffer_clear(rbuffer *rb)
{
	if (!rb)
	{
		return;
	}

	rb->head = 0;
	rb->tail = 0;
	rb->size = 0;
}

// returns the number of elements currently in the buffer
int rbuffer_size(const rbuffer *rb)
{
	if (!rb)
	{
		return 0;
	}

	return rb->size;
}
```

### Test Driver

```c
#include <stdio.h>
#include "rbuffer.h"

int main(int argc, char *argv[])
{
	rbuffer rb;

	rbuffer_init(&rb, 5);

	rbuffer_push(&rb, 10);
	rbuffer_push(&rb, 20);
	rbuffer_push(&rb, 30);
	rbuffer_push(&rb, 40);
	rbuffer_push(&rb, 50);

	int val;
	while (!rbuffer_empty(&rb))
	{
		if (rbuffer_front(&rb, &val))
		{
			printf("%d\n", val);
		}
		
		rbuffer_pop(&rb);
	}

	rbuffer_destroy(&rb);

	printf("%d %d\n", rbuffer_empty(&rb), rbuffer_full(&rb));

	return 0;
}
```

```plain
10
20
30
40
50
1 0
```



