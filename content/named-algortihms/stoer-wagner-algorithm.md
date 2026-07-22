---
title: Stoer-Wagner Algorithm
---

**Purpose:** Find the global minimum cut of a weighted, undirected graph (the minimum total weight of edges whose removal disconnects the graph), in O(V³) time, without needing a designated source/sink.

### Algorithm
1. Start with the original graph and repeat the following "minimum cut phase" until only one vertex remains:
2. **Maximum adjacency ordering:** starting from any vertex, repeatedly add the vertex most tightly connected (highest total edge weight) to the vertices already added, until all vertices are ordered.
3. The last two vertices added in this ordering, call them `s` and `t`, give a "cut-of-the-phase": the total weight of edges from `t` to all other vertices equals the minimum `s-t` cut weight for this phase (a proven property of this specific ordering).
4. Record this cut-of-the-phase value, then merge `s` and `t` into a single combined vertex (summing edge weights to shared neighbors).
5. Repeat the phase on the shrunk graph.
6. The minimum cut-of-the-phase value found across all phases is the global minimum cut.

### Code
```cpp
int stoerWagner(vector<vector<int>> graph) {
    int n = graph.size();
    vector<int> vertices(n);
    iota(vertices.begin(), vertices.end(), 0);
    int minCut = INT_MAX;

    while (vertices.size() > 1) {
        vector<int> weights(n, 0);
        vector<bool> added(n, false);
        int prev = -1, last = -1;

        for (size_t i = 0; i < vertices.size(); i++) {
            int sel = -1;
            for (int v : vertices) {
                if (!added[v] && (sel == -1 || weights[v] > weights[sel])) sel = v;
            }
            added[sel] = true;
            prev = last;
            last = sel;

            for (int v : vertices) if (!added[v]) weights[v] += graph[sel][v];
        }

        minCut = min(minCut, weights[last]);

        for (int v : vertices) {
            graph[prev][v] += graph[last][v];
            graph[v][prev] += graph[v][last];
        }
        vertices.erase(find(vertices.begin(), vertices.end(), last));
    }
    return minCut;
}
```

### Paradigm
**Greedy, combined with Decrease and Conquer.** Each phase greedily builds a maximum adjacency ordering and extracts a guaranteed cut-of-the-phase, then decreases the problem by merging two vertices — shrinking toward the base case of a single vertex.

### Complexity
- Time: O(V³)
- Space: O(V²)

### Proof of Correctness
**Cut-of-the-phase lemma:** Given a maximum adjacency ordering ending in vertices `s` then `t` (last two added), the weight of edges from `t` to the rest of the graph equals the minimum weight of any cut separating `s` and `t` specifically.

*Proof sketch:* For any `s`-`t` cut `(A, B)` with `t ∈ B`, consider the order vertices were added. One can show by induction on the ordering that every vertex `v` added before the final two has at least as much "activity" (edge weight) connecting it to already-added vertices *on both sides eventually merging toward t* as it would need to cross any candidate cut prematurely — formally, the maximum adjacency rule guarantees that `t`'s total connection to all other vertices at the end is never more than the weight of any valid `s`-`t` cut, because every vertex added right before `t` was chosen specifically for having maximum connectivity to the current set, which structurally prevents any cheaper `s`-`t` separation from being missed. (This is the core, non-trivial lemma of the algorithm — its full proof is a careful induction found in the original Stoer-Wagner paper.)

**Why merging `s` and `t` is safe:** Since the true minimum cut either separates `s` from `t` (already captured as the cut-of-the-phase) or keeps them on the same side, in the latter case the minimum cut of the *original* graph is identical to the minimum cut of the graph with `s` and `t` merged into one vertex (since any cut treating them the same way corresponds exactly, weight for weight).

**Why taking the minimum over all phases works:** Since every phase either directly captures the true global minimum cut (if it happens to separate `s` and `t` optimally) or correctly reduces to an equivalent smaller problem otherwise, taking the minimum over all `V - 1` phases is guaranteed to include the true global minimum cut at least once. ∎

### Variants / Use Cases
- **Karger's Algorithm** → randomized alternative for global min-cut, often faster in practice with repetition
- **Network reliability analysis** → global min-cut identifies the weakest link in a network's connectivity
- **Image segmentation** → min-cut based partitioning of pixel similarity graphs
- **Circuit partitioning (VLSI design)** → minimizing cross-partition wiring cost
- **Community detection** → repeatedly finding and removing min-cuts to partition graphs into clusters