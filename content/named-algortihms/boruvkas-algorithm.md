---
title: Borůvka's Algorithm
---

**Purpose:** Find a Minimum Spanning Tree (MST) of a connected, weighted, undirected graph, in O(E log V) time by growing many components in parallel across rounds.
### Algorithm
1. Start with every vertex as its own separate component.
2. In each round, for every component, find its cheapest edge that connects to a vertex outside the component.
3. Collect all these cheapest outgoing edges (one per component) and add them to the MST, using Union-Find to skip an edge if both endpoints are already in the same component (avoids duplicates when two components pick the same connecting edge).
4. Merge the components joined by the newly added edges.
5. Repeat the process on the new, smaller set of components.
6. Stop when only one component remains — the collected edges form the MST.

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

int boruvkaMST(int n, vector<array<int,3>>& edges) {
    DSU dsu(n);
    int mstCost = 0;
    int numComponents = n;

    while (numComponents > 1) {
        vector<int> cheapest(n, -1);

        for (int i = 0; i < (int)edges.size(); i++) {
            auto& [u, v, w] = edges[i];
            int pu = dsu.find(u), pv = dsu.find(v);
            if (pu == pv) continue;

            if (cheapest[pu] == -1 || edges[cheapest[pu]][2] > w) cheapest[pu] = i;
            if (cheapest[pv] == -1 || edges[cheapest[pv]][2] > w) cheapest[pv] = i;
        }

        for (int i = 0; i < n; i++) {
            if (cheapest[i] != -1) {
                auto& [u, v, w] = edges[cheapest[i]];
                if (dsu.unite(u, v)) {
                    mstCost += w;
                    numComponents--;
                }
            }
        }
    }
    return mstCost;
}
```

### Paradigm

**Greedy.** Every round, each component independently commits to its cheapest outgoing edge, relying on the cut property to guarantee these locally optimal choices are always safe to add to the MST.

### Complexity

- Time: O(E log V) — at most log V rounds since components at least halve each round, each round scans all E edges
- Space: O(V + E)

### Proof of Correctness

**Claim (Cut Property):** For any component `C` at any stage, the minimum-weight edge leaving `C` belongs to some MST.

**Proof:** Consider the cut separating `C` from the rest of the graph. Suppose the minimum-weight crossing edge `e` is not in some MST `T`. Adding `e` to `T` creates a cycle, which must cross the cut at least twice, so some other edge `e'` in `T` also crosses this cut. Since `e` is the minimum-weight crossing edge, `weight(e) ≤ weight(e')`. Swapping `e'` out for `e` gives a spanning tree with cost ≤ original, so `e` can always be included in some MST.

**Applying this to Borůvka's:** At every round, each component's cheapest outgoing edge is the minimum-weight edge across the cut separating that component from the rest of the graph (at that stage). By the cut property, each such edge is safe to add. Merging components along safe edges never creates a cycle, since edges only connect currently-separate components (enforced by the Union-Find check).

**Termination and correctness of the final result:** Each round at least halves the number of components, since every component merges with at least one other. After at most `log V` rounds, exactly one component remains, containing exactly `V - 1` edges, all proven safe — a valid MST. ∎

### Variants / Use Cases
- **Parallel/distributed MST computation** → each component's cheapest-edge search is independent, making Borůvka's naturally parallelizable unlike Prim's or Kruskal's
- **Borůvka's + Prim's hybrid** → some MST implementations run a few Borůvka rounds first to shrink the graph, then switch to Prim's
- **Randomized MST algorithms** → Borůvka's rounds are a building block in some near-linear-time randomized MST algorithms
- **VLSI circuit design / clustering** → parallel nature makes it useful in large-scale network and hardware layout problems
- **Distributed sensor networks** → components (nodes) can locally decide their cheapest link without global coordination

**Built on:** Union-Find (Disjoint Set Union).