---
title: Prim's Algorithm
---

**Purpose:** Find a Minimum Spanning Tree (MST) of a connected, weighted, undirected graph, in O(E log V) time using a priority queue.
### Algorithm
1. Start with any vertex, mark it visited, and add it to the growing MST.
2. Maintain a min-priority queue of edges connecting visited vertices to unvisited ones.
3. Repeatedly pop the smallest-weight edge from the queue.
4. If it connects to an already-visited vertex, discard it (it would form a cycle).
5. Otherwise, add this edge to the MST, mark the new vertex visited, and push all its edges to unvisited neighbors into the queue.
6. Repeat until all vertices are visited. The collected edges form the MST.

### Code
```cpp
int primsMST(int n, vector<vector<pair<int,int>>>& adj) {
    vector<bool> visited(n, false);
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    pq.push({0, 0}); // {weight, vertex}
    int mstCost = 0;

    while (!pq.empty()) {
        auto [w, u] = pq.top(); pq.pop();
        if (visited[u]) continue;

        visited[u] = true;
        mstCost += w;

        for (auto [v, wt] : adj[u]) {
            if (!visited[v]) pq.push({wt, v});
        }
    }
    return mstCost;
}
```

### Paradigm

**Greedy.** At every step it picks the smallest-weight edge crossing the visited/unvisited boundary and commits to it permanently, relying on the cut property to guarantee this local choice is always part of some global MST.

### Complexity

- Time: O(E log V)
- Space: O(V + E)

### Proof of Correctness

**Claim (Cut Property):** For any cut of the graph into two non-empty sets, the minimum-weight edge crossing that cut belongs to some MST.

**Proof of cut property:** Suppose the minimum-weight crossing edge `e = (u, v)` is _not_ in some MST `T`. Since `T` is a spanning tree, adding `e` to `T` creates a cycle, and this cycle must cross the cut at least twice (to leave and return). So there's some other edge `e'` in `T` that also crosses the cut. Since `e` is the minimum-weight crossing edge, `weight(e) ≤ weight(e')`. Removing `e'` from `T` and adding `e` instead gives a spanning tree with total weight ≤ original — so `e` can always be swapped in without increasing cost, meaning some MST contains `e`.

**Applying this to Prim's:** At every step, the visited set `S` and unvisited set `V - S` form a valid cut. The algorithm always picks the minimum-weight edge crossing this exact cut. By the cut property, this edge belongs to some MST. Since this holds at every step, and the algorithm never removes an edge once added, the final set of edges forms a valid MST.

**Termination:** Each iteration adds exactly one new vertex to the visited set, so after `V - 1` successful additions, all vertices are visited and the tree is complete with no cycles (since visited-to-visited edges are always discarded). ∎

### Variants / Use Cases

- **Prim's with adjacency matrix (O(V²))** → simpler implementation, better for dense graphs without a priority queue
- **Kruskal's Algorithm** → alternative MST approach using Union-Find, sorts all edges globally instead of growing from one vertex
- **Minimum bottleneck spanning tree** → MST edges also solve the problem of minimizing the maximum edge weight on some path
- **Network design (cabling, pipelines, circuits)** → real-world MST application to minimize total connection cost
- **Clustering (single-linkage)** → removing the largest edges from an MST partitions points into clusters
- **Steiner Tree Problem** → generalizes MST to include optional extra (Steiner) points, solved approximately using MST-based heuristics