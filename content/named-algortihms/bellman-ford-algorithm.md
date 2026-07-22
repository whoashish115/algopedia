---
title: Bellman-Ford Algorithm
---

**Purpose:** Find the shortest path from a single source to all other vertices in a weighted graph, even with negative edge weights, and detect negative-weight cycles, in O(V·E) time.

### Algorithm
1. Initialize a `dist[]` array with infinity for all vertices, except the source, which is 0.
2. Relax all edges repeatedly, `V - 1` times (where `V` is the number of vertices):
   - For each edge `(u, v, w)`, if `dist[u] + w < dist[v]`, update `dist[v] = dist[u] + w`.
3. After `V - 1` passes, all shortest paths (if no negative cycle exists) are finalized.
4. Do one more pass over all edges: if any edge can still be relaxed (`dist[u] + w < dist[v]`), a negative-weight cycle exists and shortest paths are not well-defined.

### Code
```cpp
bool bellmanFord(int n, vector<array<int,3>>& edges, int src, vector<int>& dist) {
    dist.assign(n, INT_MAX);
    dist[src] = 0;

    for (int i = 0; i < n - 1; i++) {
        for (auto& [u, v, w] : edges) {
            if (dist[u] != INT_MAX && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
            }
        }
    }

    // check for negative cycle
    for (auto& [u, v, w] : edges) {
        if (dist[u] != INT_MAX && dist[u] + w < dist[v]) {
            return false; // negative cycle exists
        }
    }
    return true;
}
```

### Paradigm
**Dynamic Programming.** `dist[v]` after `k` iterations is the optimal solution to the subproblem "shortest path to `v` using at most `k` edges," built up from the `k-1` solution by relaxing edges — a bottom-up DP over path length, unlike Dijkstra's greedy commit-and-never-revisit approach.

### Complexity
- Time: O(V·E)
- Space: O(V)

### Proof of Correctness
**Claim:** After `k` iterations of relaxing all edges, `dist[v]` is correct for every vertex `v` whose shortest path from the source uses at most `k` edges.

**Proof by induction on `k`:**

**Base case:** After 0 iterations, `dist[src] = 0` is correct for the shortest path using 0 edges (just the source itself); all other vertices are still infinity, correctly reflecting that no path using 0 edges reaches them.

**Inductive step:** Assume after `k` iterations, `dist[v]` is correct for all vertices whose shortest path uses at most `k` edges. Consider a vertex `x` whose shortest path uses exactly `k+1` edges, with the last edge being `(y, x, w)`. Since the path to `y` (the second-to-last vertex) uses at most `k` edges, `dist[y]` is already correct after `k` iterations by the inductive hypothesis. In the `(k+1)`-th iteration, since we relax *every* edge (including `(y, x, w)`), `dist[x]` gets updated to `dist[y] + w`, which is exactly the shortest path length. So `dist[x]` becomes correct after `k+1` iterations.

**Why V - 1 iterations suffice:** A shortest path in a graph with `V` vertices (assuming no negative cycles) can have at most `V - 1` edges, since a simple path visits each vertex at most once. So after `V - 1` iterations, all shortest distances are finalized.

**Negative cycle detection:** If a negative-weight cycle is reachable from the source, distances along that cycle can be decreased indefinitely by going around it repeatedly. This means even after `V - 1` iterations, at least one edge can still be relaxed further — which is exactly what the extra `V`-th check detects. ∎

### Variants / Use Cases
- **Negative cycle detection in currency arbitrage** → model exchange rates as edge weights (log-transformed) to detect profitable arbitrage loops
- **Bellman-Ford with path reconstruction** → maintain a `parent[]` array to backtrack the actual shortest path
- **SPFA (Shortest Path Faster Algorithm)** → queue-based optimization of Bellman-Ford, faster in practice on sparse graphs
- **Distance-vector routing protocols (RIP)** → real-world application, each node runs a distributed version of Bellman-Ford
- **Difference constraints systems** → solving systems of inequalities `x_j - x_i ≤ w` using shortest-path formulation
- **Detecting unreachable negative cycles** → only report cycles reachable from the source, not all cycles in the graph
