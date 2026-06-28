# Graph Traversal

## Graph

A graph consists of:

- vertices (nodes)
- edges (connections)

Example:

A -- B
| |
C -- D

---

# Depth First Search (DFS)

Definition:

Explore as deeply as possible before backtracking.

Characteristics:

- stack
- recursion
- backtracking

Example:

A
↓
B
↓
D
↑
B
↓
C

Applications:

- connected components
- cycle detection
- tree traversal
- file system traversal

Complexity:

O(V+E)

---

# Breadth First Search (BFS)

Definition:

Explore neighboring nodes before moving deeper.

Characteristics:

- queue
- level order traversal

Example:

A
B C
D E

Applications:

- shortest path
- nearest neighbor
- routing
- social graphs

Complexity:

O(V+E)

---

# DFS vs BFS

| DFS                  | BFS           |
| -------------------- | ------------- |
| stack                | queue         |
| recursion            | iteration     |
| deep first           | level first   |
| less memory          | more memory   |
| connected components | shortest path |
