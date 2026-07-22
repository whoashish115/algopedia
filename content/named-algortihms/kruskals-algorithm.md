---
title: Kruskal's Algorithm
---

**Purpose:** Find a Minimum Spanning Tree (MST) of a connected, weighted, undirected graph, in O(E log E) time by processing edges in sorted order.
### Algorithm
1. Sort all edges by weight in ascending order.
2. Initialize a Union-Find (Disjoint Set) structure with every vertex in its own set.
3. Go through the sorted edges one by one.
4. For each edge `(u, v, w)`, check if `u` and `v` are already in the same set (would form a cycle).
5. If they're in different sets, add this edge to the MST and union the two sets together.
6. If they're already in the same set, skip this edge.
7. Stop once the MST has `V - 1` edges (or all edges are processed).

### Code

```cpp
struct DSU {
    vector<int> parent, rank_;
    DSU(int n) : parent(n), rank_(n, 0) {
        for (int i = 0; i < n; i++) parent[i] = i;
    }
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }
    bool unite(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return false;
        if (rank_[px] < rank_[py]) swap(px, py);
        parent[py] = px;
        if (rank_[px] == rank_[py]) rank_[px]++;
        return true;
    }
};

int kruskalMST(int n, vector<array<int,3>>& edges) {
    sort(edges.begin(), edges.end(), [](auto& a, auto& b) {
        return a[2] < b[2];
    });

    DSU dsu(n);
    int mstCost = 0;

    for (auto& [u, v, w] : edges) {
        if (dsu.unite(u, v)) {
            mstCost += w;
        }
    }
    return mstCost;
}
```

### Paradigm
**Greedy.** It processes edges in globally sorted order and commits to the smallest edge that doesn't create a cycle, relying on the cut/cycle property to guarantee each locally safe choice is part of some global MST.

### Complexity
- Time: O(E log E) — dominated by sorting edges; Union-Find operations are nearly O(1) amortized
- Space: O(V + E)

### Proof of Correctness

**Claim (Cycle Property):** For any cycle in the graph, the maximum-weight edge in that cycle is _not_ part of any MST — equivalently, an edge that would create a cycle when added to a growing forest is safe to skip.

**Proof:** Suppose edge `e` is skipped because `u` and `v` are already connected (in the same set), meaning a path already exists between them using only smaller-or-equal-weight edges (since edges are processed in sorted order). Adding `e` would create a cycle where `e` is the maximum-weight edge in that cycle. Any spanning tree containing `e` could remove `e` and reconnect via the existing path without increasing total weight — so `e` is never required in an MST, and skipping it is safe.

**Claim (Cut Property, applied to accepted edges):** When edge `e = (u, v, w)` is accepted because `u` and `v` are in different sets, `e` is the minimum-weight edge crossing the cut between `u`'s current component and the rest of the graph reachable at this stage (since all smaller edges were already processed and either accepted or correctly rejected as cycle-forming). By the cut property, this minimum-weight crossing edge belongs to some MST, so accepting it is safe.

**Termination:** Each accepted edge merges two previously separate components into one, so after exactly `V - 1` acceptances, all vertices belong to a single component with no cycles — a valid MST. ∎

### Variants / Use Cases
- **Prim's Algorithm** → alternative MST approach that grows a single tree from one vertex instead of processing edges globally
- **Kruskal's with Union-Find by rank/path compression** → nearly O(1) amortized per operation, making sorting the true bottleneck
- **Maximum Spanning Tree** → sort edges in descending order instead of ascending
- **Minimum spanning forest** → same algorithm on disconnected graphs, naturally produces one tree per component
- **Clustering (single-linkage)** → stopping Kruskal's early (before `V-1` edges) partitions points into clusters
- **Network design under budget constraints** → useful when edges must be added incrementally while tracking connectivity

**Built on:** Union-Find (Disjoint Set Union).