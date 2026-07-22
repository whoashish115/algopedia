---
title: Gomory-Hu Algorithm
---

**Purpose:** Build a single weighted tree on `V` vertices that encodes the minimum `s-t` cut value between *every* pair of vertices in a graph, using only `V - 1` max-flow computations instead of O(V²).

### Algorithm
1. Start with a tree containing all vertices, initially all connected to some arbitrary root (structure will be rebuilt as edges are found).
2. For each vertex `i` from 1 to `V - 1` (skipping the root), let `p(i)` be `i`'s current parent in the tree.
3. Compute the minimum `s-t` cut between `i` and `p(i)` in the *original* graph, and let `f` be this cut's value and `S` be the set of vertices on `i`'s side of this cut.
4. Set the tree edge weight between `i` and `p(i)` to `f`.
5. For every other vertex `j` whose current parent is `p(i)` and which lies in `S`, redirect `j`'s parent to `i` instead (since the cut separates them together with `i`).
6. If `p(i)` itself has a parent outside `S`... continue: if `p(p(i))` is in `S`, adjust connections accordingly to maintain tree correctness.
7. After processing all vertices, the resulting tree is the Gomory-Hu tree: the minimum cut between any two vertices `u, v` equals the minimum edge weight along the tree path between them.

### Code
```cpp
// Requires a maxFlowMinCut(n, graph, s, t) function returning {flowValue, sourceSideSet}
vector<int> gomoryHuTree(int n, vector<vector<int>>& capacity) {
    vector<int> parent(n, 0);
    vector<int> treeWeight(n, 0);

    for (int i = 1; i < n; i++) {
        auto graphCopy = capacity; // fresh capacities each time
        auto [flow, sourceSide] = maxFlowMinCut(n, graphCopy, i, parent[i]);
        treeWeight[i] = flow;

        for (int j = i + 1; j < n; j++) {
            if (parent[j] == parent[i] && sourceSide[j]) {
                parent[j] = i;
            }
        }
        // if parent[i]'s own parent ends up on i's side, swap them
        if (sourceSide[parent[parent[i]]] && parent[i] != 0) {
            parent[i] = parent[parent[i]];
            // (full implementations also swap treeWeight bookkeeping here)
        }
    }
    return parent; // combined with treeWeight, defines the Gomory-Hu tree
}
```

### Paradigm
**Transform and Conquer combined with Greedy.** The algorithm transforms the O(V²)-pairwise-cuts problem into V−1 carefully chosen max-flow computations by exploiting cut structure, greedily fixing each vertex's tree edge using only one min-cut computation against its current parent.

### Complexity
- Time: O(V) max-flow computations, so O(V) × (max-flow cost) — typically O(V⁴) to O(V · E · maxflow) depending on the flow algorithm used
- Space: O(V²) for the capacity matrix

### Proof of Correctness
**Core cut-equivalence property:** The Gomory-Hu tree has the property that for any two vertices `u, v`, the minimum edge weight on the tree path between them equals the true minimum `u-v` cut value in the original graph — and the two sides of the minimum tree-path edge, when removed, correspond to (a valid) actual minimum cut separating `u` and `v` in the original graph.

**Why processing against the current parent suffices (sketch):** The proof relies on the *uncrossing* property of minimum cuts: given minimum cuts for two different pairs that "cross" (partially overlap), one can always construct two new minimum cuts that don't cross and have the same total weight, by taking unions/intersections of the crossing sets. This lets the algorithm always resolve each vertex's relationship to the tree being built so far using a cut against its *current* representative parent, without needing to separately verify consistency against every other vertex — the uncrossing property guarantees the partial tree remains consistent as more vertices are processed.

**Correctness of pairwise cut recovery:** Once the full tree is built, for any pair `(u, v)`, consider the unique path between them in the tree. Removing the minimum-weight edge on this path splits the tree (and correspondingly the vertex set) into two parts; this split corresponds to an actual cut in the original graph with weight equal to that minimum tree edge. By construction (via the uncrossing argument applied inductively), no cheaper `u-v` cut can exist, so this is exactly the minimum `u-v` cut. ∎ *(Full rigorous proof is intricate — see Gomory & Hu's original 1961 paper or Gusfield's simplified construction.)*

### Variants / Use Cases
- **Gusfield's simplification** → avoids graph contraction between steps, computing all cuts against the original graph directly, easier to implement
- **All-pairs minimum cut queries** → the primary use case, answering any pair's min-cut in O(1) via a tree-path minimum after O(V) preprocessing
- **Network reliability analysis** → identifying the weakest link between any two nodes in a network
- **Clustering via tree cuts** → repeatedly cutting the Gomory-Hu tree's minimum edges partitions the graph into well-separated clusters
- **VLSI and circuit partitioning** → minimizing cross connections between arbitrary component pairs
