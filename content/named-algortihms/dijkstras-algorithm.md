---
title: Dijkstra's Algorithm
---

**Purpose:** Find the shortest path from a single source to all other vertices in a weighted graph with non-negative edge weights, in O((V + E) log V) time using a priority queue.
### Algorithm
1. Initialize a `dist[]` array with infinity for all vertices, except the source, which is 0.
2. Use a min-priority queue (min-heap) and push the source with distance 0.
3. While the priority queue is not empty:
   - Pop the vertex `u` with the smallest current distance.
   - If this popped distance is stale (greater than `dist[u]`), skip it.
   - For each neighbor `v` of `u` with edge weight `w`, check if going through `u` gives a shorter path: `dist[u] + w < dist[v]`.
   - If so, update `dist[v]` and push `(dist[v], v)` into the priority queue.
4. Continue until the queue is empty. `dist[]` now holds the shortest distance from the source to every vertex.

### Code
```cpp
vector<int> dijkstra(int n, vector<vector<pair<int,int>>>& adj, int src) {
    vector<int> dist(n, INT_MAX);
    dist[src] = 0;
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    pq.push({0, src});

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;

        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

### Paradigm
**Greedy.** At every step it commits to the vertex with the smallest tentative distance and never revisits that choice, relying on the fact that non-negative weights guarantee the locally best choice is also globally optimal — the defining property of a greedy algorithm.

### Complexity
- Time: O((V + E) log V)
- Space: O(V + E)

### Proof of Correctness
**Claim:** When a vertex `u` is popped from the priority queue, `dist[u]` is already its true shortest distance from the source.

**Proof by induction on the order vertices are popped:**

**Base case:** The source is popped first with `dist = 0`, which is trivially its shortest distance.

**Inductive step:** Assume every vertex popped before `u` has its correct shortest distance finalized. Suppose, for contradiction, that `dist[u]` when popped is *not* the true shortest distance — meaning the real shortest path to `u` is shorter and must pass through some other vertex `x` where `dist[x] + weight(x,u) < dist[u]`.

Since all edge weights are non-negative, any vertex `x` on the true shortest path to `u` must have a shortest distance `dist[x] ≤ dist[u]`. This means `x` would have been popped before or at the same time as `u` (since the priority queue always pops the smallest tentative distance first). By the inductive hypothesis, `x`'s shortest distance was already finalized when it was popped, and at that point, the algorithm relaxes all of `x`'s edges — including the one to `u` — updating `dist[u]` accordingly and pushing it into the queue.

This means the correct, shorter `dist[u]` would already be in the queue before `u` is popped with a wrong value, contradicting the assumption that `u` was popped with an incorrect (larger) distance. Hence, no such `x` can exist, and `dist[u]` is correct when popped. ∎

*(Note: this proof relies critically on non-negative edge weights — negative weights break the "closer vertices are popped first" guarantee, which is why Dijkstra fails on graphs with negative edges.)*

### Variants / Use Cases
- **Dijkstra with path reconstruction** → maintain a `parent[]` array to backtrack the actual shortest path
- **Bidirectional Dijkstra** → run simultaneously from source and target, meeting in the middle for faster point-to-point queries
- **A\* Search** → Dijkstra augmented with a heuristic to prioritize promising directions (used in pathfinding/games)
- **Dijkstra on graphs with negative weights (doesn't work directly)** → use Bellman-Ford instead
- **k-shortest paths** → extended versions (e.g., Eppstein's algorithm) build on Dijkstra's structure
- **Network routing protocols (OSPF)** → real-world application of shortest-path computation
