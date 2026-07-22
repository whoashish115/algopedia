---
title: Edmonds-Karp Algorithm
---

**Purpose:** Compute the maximum flow from source to sink in a flow network, in O(VE²) time, by always augmenting along the shortest (fewest-edges) path.

### Algorithm
1. Initialize flow on every edge to 0.
2. Build the residual graph (forward edges with remaining capacity, backward edges representing flow that can be undone).
3. Run BFS on the residual graph from source to sink to find the shortest augmenting path (fewest edges).
4. If no path exists, stop — the current flow is maximum.
5. Otherwise, find the bottleneck capacity (minimum residual capacity) along this path.
6. Push flow equal to the bottleneck along the path: decrease forward residual capacities, increase backward residual capacities.
7. Repeat from step 3.

### Code
```cpp
int edmondsKarp(int n, vector<vector<int>>& cap, int src, int sink) {
    int maxFlow = 0;

    while (true) {
        vector<int> parent(n, -1);
        parent[src] = src;
        queue<int> q;
        q.push(src);

        while (!q.empty() && parent[sink] == -1) {
            int u = q.front(); q.pop();
            for (int v = 0; v < n; v++) {
                if (parent[v] == -1 && cap[u][v] > 0) {
                    parent[v] = u;
                    q.push(v);
                }
            }
        }

        if (parent[sink] == -1) break;

        int bottleneck = INT_MAX;
        for (int v = sink; v != src; v = parent[v])
            bottleneck = min(bottleneck, cap[parent[v]][v]);

        for (int v = sink; v != src; v = parent[v]) {
            cap[parent[v]][v] -= bottleneck;
            cap[v][parent[v]] += bottleneck;
        }
        maxFlow += bottleneck;
    }
    return maxFlow;
}
```

### Paradigm
**Greedy.** It repeatedly commits to pushing the maximum possible flow along the current shortest augmenting path and never undoes a completed augmentation, relying on the max-flow min-cut theorem to guarantee this converges to the optimum.

### Complexity
- Time: O(VE²)
- Space: O(V²) for the capacity matrix

### Proof of Correctness
**Correctness of the final flow value (Max-Flow Min-Cut):** The algorithm terminates when BFS can no longer reach the sink from the source in the residual graph. Let `S` be the set of vertices reachable from the source at termination, and `T` the rest. Every edge from `S` to `T` must be fully saturated (otherwise it would still be reachable), and every edge from `T` to `S` must carry zero flow (otherwise its residual back-edge would make the `T` side reachable). So the flow across this cut equals the cut's capacity — meaning the current flow equals the capacity of some cut, which by weak duality must be the minimum cut, and a flow equal to some cut's capacity is necessarily maximum.

**Why O(VE²) iterations suffice:** Each augmentation saturates at least one edge on the shortest path. A classical distance-labeling argument shows that any fixed edge can become the bottleneck at most O(V) times over the whole run, because each time it's saturated, the shortest-path distance from source to either of its endpoints must strictly increase before it can be used as a bottleneck again (BFS finds strictly non-decreasing shortest path lengths across iterations). With O(E) edges each saturating O(V) times, total augmentations are O(VE), each costing O(E) for the BFS — giving O(VE²) overall. ∎

### Variants / Use Cases
- **Ford-Fulkerson (generic, DFS-based)** → same method, but path choice isn't BFS-restricted, losing the polynomial bound guarantee
- **Bipartite matching via max flow** → model as a flow network with unit capacities, giving maximum matching
- **Min-cut computation** → the reachable set `S` at termination directly gives a minimum cut
- **Project selection / image segmentation (min-cut/max-flow duality)** → common real-world applications
- **Circulation with lower bounds** → extended flow formulations built on the same augmenting path idea
