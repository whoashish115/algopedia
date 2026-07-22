---
title: Johnson's Algorithm
---

**Purpose:** Find the shortest path between every pair of vertices in a sparse graph with possibly negative edge weights (but no negative cycles), in O(V² log V + VE) time — faster than Floyd-Warshall on sparse graphs.
### Algorithm
1. Add a virtual source vertex connected to every other vertex with edge weight 0.
2. Run Bellman-Ford once from this virtual source to compute `h[v]`, the shortest distance from the virtual source to every vertex `v`. This also detects negative-weight cycles — abort if one exists.
3. Reweight every original edge `(u, v, w)` using `w' = w + h[u] - h[v]`. This makes all edge weights non-negative while preserving which path is shortest.
4. Remove the virtual source. Run Dijkstra from every vertex `u` on the reweighted graph to get `d'(u, v)`, the shortest reweighted distance to every other vertex.
5. Convert back to real distances: `d(u, v) = d'(u, v) - h[u] + h[v]`.
### Code

```cpp
vector<vector<int>> johnsonsAlgorithm(int n, vector<array<int,3>>& edges) {
    vector<array<int,3>> bfEdges = edges;
    for (int v = 0; v < n; v++) bfEdges.push_back({n, v, 0});

    vector<int> h(n + 1, INT_MAX);
    h[n] = 0;
    for (int i = 0; i < n; i++) {
        for (auto& [u, v, w] : bfEdges) {
            if (h[u] != INT_MAX && h[u] + w < h[v]) h[v] = h[u] + w;
        }
    }
    for (auto& [u, v, w] : bfEdges) {
        if (h[u] != INT_MAX && h[u] + w < h[v]) throw runtime_error("Negative cycle");
    }

    vector<vector<pair<int,int>>> adj(n);
    for (auto& [u, v, w] : edges) {
        adj[u].push_back({v, w + h[u] - h[v]});
    }

    vector<vector<int>> dist(n, vector<int>(n, INT_MAX));
    for (int src = 0; src < n; src++) {
        vector<int> d(n, INT_MAX);
        d[src] = 0;
        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
        pq.push({0, src});
        while (!pq.empty()) {
            auto [du, u] = pq.top(); pq.pop();
            if (du > d[u]) continue;
            for (auto [v, w] : adj[u]) {
                if (d[u] + w < d[v]) {
                    d[v] = d[u] + w;
                    pq.push({d[v], v});
                }
            }
        }
        for (int dst = 0; dst < n; dst++) {
            if (d[dst] != INT_MAX) dist[src][dst] = d[dst] - h[src] + h[dst];
        }
    }
    return dist;
}
```

### Paradigm

**Transform and Conquer.** The core idea is instance simplification: reweight the graph via `h[]` to eliminate negative edges, transforming a hard instance (negative weights, Dijkstra unusable) into an easier equivalent one (non-negative weights) solvable with a faster algorithm.

### Complexity

- Time: O(V² log V + VE) — one Bellman-Ford run (O(VE)) plus V runs of Dijkstra (O(V log V + E) each)
- Space: O(V²) for the output distance matrix

### Proof of Correctness

**Part 1 — Reweighting preserves shortest paths:** For any path `P` from `u` to `v` passing through vertices `u = p0, p1, ..., pk = v`, its reweighted length telescopes:

```
Σ w'(pi, pi+1) = Σ [w(pi, pi+1) + h[pi] - h[pi+1]] = Σ w(pi, pi+1) + h[u] - h[v]
```

Every path between the same pair `(u, v)` shifts by the exact same constant `h[u] - h[v]`, so the relative order of path lengths — and therefore which path is shortest — is unchanged. This means `d(u,v) = d'(u,v) - h[u] + h[v]` recovers the true shortest distance.

**Part 2 — Reweighted edges are non-negative:** Since `h[v]` is the shortest distance from the virtual source to `v`, and the virtual source connects to every vertex with weight 0, the shortest-path property guarantees the triangle inequality `h[v] ≤ h[u] + w(u,v)` for every edge `(u,v,w)`. Rearranging gives `w(u,v) + h[u] - h[v] ≥ 0`, i.e., every reweighted edge `w'` is non-negative — exactly what Dijkstra requires to run correctly.

**Part 3 — Negative cycle detection:** Bellman-Ford's own correctness guarantees it detects any negative cycle reachable from the virtual source, which — since the virtual source connects to all vertices — covers any negative cycle in the graph. ∎

### Variants / Use Cases

- **Sparse graph all-pairs shortest paths** → preferred over Floyd-Warshall when `E << V²`, since Johnson's is faster on sparse graphs
- **Currency arbitrage detection across many pairs** → reweighting + Dijkstra scales better than repeated Bellman-Ford
- **Re-run Dijkstra incrementally** → if only a few edges change, only affected sources need re-running instead of recomputing `h[]` and all pairs from scratch
- **Distributed/road network routing** → used in systems needing many-to-many shortest paths where graphs are large but sparse
- **A* with landmarks (ALT algorithm)** → related reweighting/potential-function idea, using precomputed distances as heuristics instead of correctness-preserving transforms

**Built on:** Bellman-Ford + Dijkstra.