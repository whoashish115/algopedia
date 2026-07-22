---
title: Ford-Fulkerson Algorithm
---

**Purpose:** Compute the maximum flow from source to sink in a flow network by repeatedly finding any augmenting path in the residual graph, in O(E · maxFlow) time for integer capacities.

### Algorithm
1. Initialize flow on every edge to 0.
2. Build the residual graph from current flow (forward edges with remaining capacity, backward edges representing flow that can be undone).
3. Search for any path from source to sink in the residual graph (commonly via DFS).
4. If no path exists, stop — the current flow is maximum.
5. Otherwise, find the bottleneck capacity (minimum residual capacity) along this path.
6. Push flow equal to the bottleneck along the path, updating forward and backward residual capacities.
7. Repeat from step 3.

### Code
```cpp
bool dfsFindPath(int u, int sink, vector<vector<int>>& cap, vector<int>& parent, vector<bool>& visited) {
    if (u == sink) return true;
    visited[u] = true;
    for (int v = 0; v < (int)cap.size(); v++) {
        if (!visited[v] && cap[u][v] > 0) {
            parent[v] = u;
            if (dfsFindPath(v, sink, cap, parent, visited)) return true;
        }
    }
    return false;
}

int fordFulkerson(int n, vector<vector<int>>& cap, int src, int sink) {
    int maxFlow = 0;

    while (true) {
        vector<int> parent(n, -1);
        vector<bool> visited(n, false);
        parent[src] = src;
        if (!dfsFindPath(src, sink, cap, parent, visited)) break;

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
**Greedy.** It commits to pushing flow along whatever augmenting path is found next and never revisits that decision, relying on the max-flow min-cut theorem for correctness regardless of path choice.

### Complexity
- Time: O(E · maxFlow) with integer capacities (each augmentation increases flow by at least 1)
- Space: O(V²) for the capacity matrix

### Proof of Correctness
**Correctness of the final flow value:** The algorithm terminates when no path from source to sink exists in the residual graph. Let `S` be the set of vertices reachable from the source at that point, and `T` the rest. Every edge crossing from `S` to `T` must be fully saturated (else it would be usable to extend reachability), and every edge from `T` to `S` must carry zero flow (else its reverse residual edge would extend reachability). So the net flow across this cut equals the cut's capacity. Since flow value can never exceed any cut's capacity (weak duality), and here it equals one, the current flow must be maximum. This holds regardless of which augmenting path was chosen at each step — correctness doesn't depend on path selection strategy, only termination does.

**Why termination requires integer capacities:** Each augmentation increases total flow by at least 1 (the bottleneck, which is a positive integer if all capacities are integers). Since flow is bounded above by the min-cut capacity, the number of augmentations is finite. With irrational or adversarially chosen real-valued capacities, generic path selection can fail to terminate or converge to the wrong value — this is exactly why Edmonds-Karp restricts path choice to BFS shortest paths, guaranteeing polynomial termination. ∎

### Variants / Use Cases
- **Edmonds-Karp** → restrict path selection to BFS shortest paths, guaranteeing polynomial O(VE²) time regardless of capacity values
- **Dinic's Algorithm** → level graphs + blocking flow, much faster in practice and asymptotically
- **Capacity scaling** → prioritize augmenting paths with large bottleneck capacity first, improving worst-case bounds
- **Bipartite matching** → unit-capacity flow network models maximum matching directly
- **Min-cut computation** → the reachable set at termination gives a minimum cut directly
