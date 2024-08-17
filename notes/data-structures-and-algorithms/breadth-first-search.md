[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Data Structures & Algorithms</a> > Breadth-First Search (BFS)

# Breadth-First Search (BFS)



## Breadth-First Search (BFS)

* The BFS algorithm systematically explores a graph or tree **level-by-level**, ensuring that nodes at the same depth are visited before moving on to deeper levels.
* This property makes BFS particularly useful for finding the shortest path between nodes in unweighted graphs or to explore all nodes in a connected component of a graph.

### Algorithm

1. Initialize a queue (FIFO) data structure to keep track of nodes to be visited. Enqueue the starting node into the queue.
2. Initialize a set or array to keep track of visited nodes to avoid revisiting them. Mark the starting node as visited.
3. While the queue is not empty: 
   1. Dequeue a node from the front of the queue.
   2. Process the node (print its value, perform some operation, etc.)
   3. Enqueue all unvisited neighbors of the current node into the queue and mark them as visited.

4. Continue the process until the queue becomes empty, meaning all nodes have been visited.

### Pseudo-code

```plain
BFS(Graph, startNode)
	queue = new Queue()
	visited = new Set()
	
	queue.euqueue(startNode)
	visited.add(startNode)
	
	while queue is not empty:
		currentNode = queue.dequeue()
		process(currentNode)	// Process the node (e.g., print its value)
		
		for each neighbor in neighbors of currentNode:
			if neighbor is not in visited:
				queue.enqueue(neighbor)
				visited.add(neighbor)
```

> In this algorithm,
>
> * `Graph` represents the graph or data structure you're traversing
> * `startNode` is the initial node from which you want to start the traversal
> * `neighbors of currentNode` refers to the neighboring nodes of the current node that are connected by edges. 



## Code (C++)

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <unordered_set>

class Graph
{
public:
	Graph(int numNodes);
	void addEdge(int from, int to);
	void BFS(int startNode);

private:
	std::vector<std::vector<int>> adjacencyList;
};

Graph::Graph(int numNodes)
{
	adjacencyList.resize(numNodes);
}

void Graph::addEdge(int from, int to)
{
	adjacencyList[from].push_back(to);
}

void Graph::BFS(int startNode)
{
	std::queue<int> queue;
	std::unordered_set<int> visited;

	queue.push(startNode);
	visited.insert(startNode);

	while (!queue.empty())
	{
		int currentNode = queue.front();
		queue.pop();

		std::cout << currentNode << " ";	// Process the node

		for (int neighbor : adjacencyList[currentNode])
		{
			if (visited.find(neighbor) == visited.end())
			{
				queue.push(neighbor);
				visited.insert(neighbor);
			}
		}
	}

	std::cout << std::endl;
}

int main(int argc, char *argv[])
{
	Graph graph(6);

	graph.addEdge(0, 1);
	graph.addEdge(0, 2);
	graph.addEdge(1, 3);
	graph.addEdge(2, 4);
	graph.addEdge(3, 5);

	std::cout << "BFS traversal starting from node 0: ";
	graph.BFS(0);

	return 0;
}
```

```plain
BFS traversal starting from node 0: 0 1 2 3 4 5 
```



## Code (C)

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node_
{
	int data;
	struct Node_ *next;
} Node;

typedef struct Graph_
{
	int numNodes;
	Node **adjacencyList;
} Graph;

Node *createNode(int data)
{
	Node *newNode = (Node *)malloc(sizeof(Node));
	newNode->data = data;
	newNode->next = NULL;
	return newNode;
}

Graph *createGraph(int numNodes)
{
	Graph *graph = (Graph *)malloc(sizeof(Graph));
	graph->numNodes = numNodes;
	graph->adjacencyList = (Node **)malloc(numNodes * sizeof(Node*));
	for (int i = 0; i < numNodes; ++i)
	{
		graph->adjacencyList[i] = NULL;
	}

	return graph;
}

void addEdge(Graph *graph, int from, int to)
{
	Node *newNode = createNode(to);
	newNode->next = graph->adjacencyList[from];
	graph->adjacencyList[from] = newNode;
}

void BFS(Graph *graph, int startNode)
{
	int *visited = (int *)calloc(graph->numNodes, sizeof(int));
	Node *queue[graph->numNodes];
	int front = 0; int rear = 0;

	queue[rear++] = createNode(startNode);
	visited[startNode] = 1;

	while (front < rear)
	{
		Node *currNode = queue[front++];
		printf("%d ", currNode->data);	// Process the node

		Node *neighbor = graph->adjacencyList[currNode->data];
		while (neighbor)
		{
			if (!visited[neighbor->data])
			{
				queue[rear++] = createNode(neighbor->data);
				visited[neighbor->data] = 1;
			}
			neighbor = neighbor->next;
		}
	}

	printf("\n");
	free(visited);
}

int main(int argc, char *argv[])
{
	Graph *graph = createGraph(6);

	addEdge(graph, 0, 1);
	addEdge(graph, 0, 2);
	addEdge(graph, 1, 3);
	addEdge(graph, 2, 4);
	addEdge(graph, 3, 5);

	printf("BFS traversal starting from 0: ");
	BFS(graph, 0);

	return 0;
}
```

```plain
BFS traversal starting from 0: 0 2 1 4 3 5 
```

> The order of traversal in BFS can vary depending on the order in which neighbors are added to the queue. Both orders are correct as long as they follow the BFS traversal rules.
>
> In the output "0 2 1 4 3 5", the BFS algorithm is traversing the graph layer by layer, but the order of adding neighbors to the queue might be slightly different. For example, in this case, after visiting node 0, the algorithm enqueues nodes 1 and 2. Node 2 is dequeued before node 1, which is why you see node 2 being visited before node 1.
>
> So, the BFS algorithm is still correctly traversing the graph, but the order of neighbors' processing might differ based on implementation details such as the queue's behavior and order of enqueuing nodes. Both "0 1 2 3 4 5" and "0 2 1 4 3 5" are valid BFS traversal orders for the given graph and its edges.
