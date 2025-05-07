[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Stacks

# Stacks



## Stack Using Singly-Linked List (C++)

* `push()` and `pop()` operations are designed to be done on the head of the list since both of their time complexity is O(1).



<img src="./img/stack-using-singly-linked-list.png" alt="stack-using-singly-linked-list" width="250">



### Interface

```cpp
#ifndef STACK_HPP
#define STACK_HPP

class node
{
public:
	node(int val) : data(val), p_next(nullptr) {}

	int data;
	node *p_next;
};

class stack
{
public:
	stack();
	~stack();
	void push(const int val);
	void pop();
	int top() const;
	bool empty() const;
	int size() const;
	void clear();

private:
	node *p_top;
	int cnt;
};

#endif
```

### Implementation

```cpp
#include "stack.hpp"
#include <stdexcept>

stack::stack()
	: p_top(nullptr), cnt(0)
{
	// Do nothing
}

stack::~stack()
{
	while (!empty())
	{
		pop();
	}
}

void stack::push(const int val)
{
	node *p_new = new node(val);
	p_new->p_next = p_top;
	p_top = p_new;
	++cnt;
}

void stack::pop()
{
	if (empty())
	{
		throw std::runtime_error("Stack underflow");
	}

	node *p_del = p_top;
	p_top = p_top->p_next;
	delete p_del;
	--cnt;
}

int stack::top() const
{
	if (empty())
	{
		throw std::runtime_error("Stack is empty");
	}

	return p_top->data;
}

bool stack::empty() const
{
	return 0 == cnt;
}

int stack::size() const
{
	return cnt;
}

void stack::clear()
{
	while (nullptr != p_top)
	{
		node *p_del = p_top;
		p_top = p_top->p_next;
		delete p_del;
	}

	cnt = 0;
}
```

### Test Driver

```cpp
#include <iostream>
#include "stack.hpp"

int main(int argc, char *argv[])
{
	stack s;

	s.push(1);
	s.push(2);
	s.push(3);
	s.push(4);
	s.push(5);
	
	std::cout << s.top() << std::endl;		// 5
	std::cout << s.size() << std::endl;		// 5
	std::cout << s.empty() << std::endl;	// 0

	s.clear();
	std::cout << s.size() << std::endl;		// 0

	return 0;
}
```

```plain
5
5
0
0
```



## Stack Using Singly-Linked List (C)

### Interface

```c
#ifndef STACK_H
#define STACK_H

#include <stdbool.h>

typedef struct node
{
	int data;
	struct node *p_next;
} node;

typedef struct
{
	node *p_top;
	int cnt;
} stack;

// Public interface
void stack_init(stack *s);
void stack_push(stack *s, const int val);
void stack_pop(stack *s);
int stack_top(const stack *s);
bool stack_empty(const stack *s);
int stack_size(const stack *s);
void stack_clear(stack *s);

#endif
```

### Implementation

```c
#include "stack.h"
#include "stdio.h"
#include "stdlib.h"

void stack_init(stack *s)
{
	s->p_top = NULL;
	s->cnt = 0;
}

void stack_push(stack *s, const int val)
{
	node *p_new = (node *)malloc(sizeof(node));

	if (!p_new)
	{
		fprintf(stderr, "Failed to allocate memory for new node\n");	
		exit(EXIT_FAILURE);
	}

	p_new->data = val;
	p_new->p_next = s->p_top;
	s->p_top = p_new;
	s->cnt++;
}

void stack_pop(stack *s)
{
	if (stack_empty(s))
	{
		fprintf(stderr, "Stack underflow\n");
		exit(EXIT_FAILURE);
	}

	node *p_del = s->p_top;
	s->p_top = s->p_top->p_next;
	free(p_del);
	s->cnt--;
}

int stack_top(const stack *s)
{
	if (stack_empty(s))
	{
		fprintf(stderr, "Stack is empty\n");
		exit(EXIT_FAILURE);
	}

	return s->p_top->data;
}

bool stack_empty(const stack *s)
{
	return 0 == s->cnt;
}

int stack_size(const stack *s)
{
	return s->cnt;
}

void stack_clear(stack *s)
{
	while (s->p_top)
	{
		stack_pop(s);
	}
}
```

### Test Driver

```c
5
0
5
4
4
1
0
```

