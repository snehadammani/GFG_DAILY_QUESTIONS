# Shortest Path in 1-2 Graph

## Problem Statement

Given a weighted undirected graph with `V` vertices numbered from `0` to `V - 1`, where each edge has a weight of either **1** or **2**, find the shortest distance from a source vertex `src` to a destination vertex `dest`.

If the destination cannot be reached from the source, return **-1**.

---

## Approach

This solution uses **Dijkstra's Algorithm** with a **Min Heap (Priority Queue)**.

### Algorithm
1. Build an adjacency list from the given edges.
2. Initialize the distance of every vertex as infinity.
3. Set the distance of the source vertex to `0`.
4. Use a priority queue to always process the vertex with the minimum current distance.
5. Relax all adjacent edges.
6. If a shorter path is found, update the distance and push the vertex back into the priority queue.
7. Return the shortest distance to the destination, or `-1` if it is unreachable.

---

## Data Structures Used

- Adjacency List
- Min Heap (Priority Queue)
- Distance Array

---

## Complexity Analysis

- **Time Complexity:** `O((V + E) log V)`
- **Space Complexity:** `O(V + E)`

where:
- `V` = Number of vertices
- `E` = Number of edges

---

## Example

### Input

```text
V = 4
edges = [[0,1,1],[0,2,2],[2,3,1],[1,2,1],[1,3,2]]
src = 0
dest = 3
```

### Output

```text
3
```

### Explanation

There are two shortest paths:

- `0 → 1 → 3` with cost `1 + 2 = 3`
- `0 → 2 → 3` with cost `2 + 1 = 3`

Hence, the shortest distance is **3**.

---

## Python Solution

```python
import heapq

class Solution:
    def shortestPath(self, V: int, src: int, dest: int, edges: list[list[int]]) -> int:
        adj = [[] for _ in range(V)]

        for u, v, w in edges:
            adj[u].append((v, w))
            adj[v].append((u, w))

        INF = float('inf')
        dist = [INF] * V
        dist[src] = 0

        pq = [(0, src)]

        while pq:
            d, u = heapq.heappop(pq)

            if d > dist[u]:
                continue

            if u == dest:
                return d

            for v, w in adj[u]:
                if dist[v] > d + w:
                    dist[v] = d + w
                    heapq.heappush(pq, (dist[v], v))

        return -1 if dist[dest] == INF else dist[dest]
```

---

## Key Concepts

- Graphs
- Shortest Path
- Dijkstra's Algorithm
- Priority Queue (Min Heap)
- Greedy Algorithm
- Adjacency List

---

## Tags

`Graph` `Dijkstra` `Shortest Path` `Priority Queue` `Heap` `Greedy` `Python` `GFG`
