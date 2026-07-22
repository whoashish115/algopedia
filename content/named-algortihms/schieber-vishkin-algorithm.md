---
title: Schieber-Vishkin Algorithm
---

**Purpose:** Answer Lowest Common Ancestor (LCA) queries online in true O(1) time per query, after O(N) preprocessing, using bit-manipulation tricks instead of sparse tables.
### Algorithm
1. Run a DFS on the tree, assigning each vertex an in-order-like traversal number and computing, for each vertex, the lowest set bit position that's unique among all its ancestors up to that point (this builds an "indicator" per vertex).
2. Compute for each vertex a value combining its subtree's DFS-number range and this indicator bit, encoding enough information to identify ancestor relationships using only bitwise operations.
3. For a query `(u, v)`, compute the bitwise structure that identifies the "highest" bit at which `u` and `v`'s ancestor-indicators diverge — this pinpoints which of `u` or `v` is "closer" to the actual LCA in the encoded structure.
4. Use precomputed per-vertex jump tables (based on the same bit tricks used in binary lifting, but resolved via O(1) bit operations rather than O(log N) jumps) to walk directly to the ancestor at the required level.
5. Return this ancestor as the LCA.

### Code
```cpp
// Simplified sketch — full Schieber-Vishkin involves intricate bit-trick precomputation.
// This shows the high-level structure; production implementations follow the original paper closely.
struct SchieberVishkin {
    vector<int> parent, indexNum, ancestorMask, head, inLabel;
    vector<int> preOrder;
    int timer = 0;

    void dfs(int u, vector<vector<int>>& children) {
        indexNum[u] = timer++;
        inLabel[u] = indexNum[u]; // simplified placeholder for the true lowest-bit-based label
        for (int c : children[u]) {
            dfs(c, children);
            // propagate lowest set bit info upward (bit trick omitted for brevity)
            if ((inLabel[c] & -inLabel[c]) > (inLabel[u] & -inLabel[u])) inLabel[u] = inLabel[c];
        }
    }

    int lca(int u, int v) {
        // O(1) query using precomputed labels + ancestor masks (full bit-trick logic omitted)
        // Conceptually: find highest divergence bit, then jump both u and v to that level.
        return -1; // placeholder — see original paper for exact O(1) formulas
    }
};
```

### Paradigm
**Transform and Conquer.** The algorithm encodes the entire ancestor structure of the tree into compact bit-level labels during preprocessing, transforming the LCA query into simple, constant-time bitwise operations rather than any traversal or table lookup at query time.

### Complexity
- Time: O(N) preprocessing, O(1) per query
- Space: O(N)

### Proof of Correctness
**Core idea (informal):** Every vertex is assigned a label derived from a DFS numbering combined with a specific bit-selection rule that guarantees: for any two vertices `u` and `v`, the bit position where their ancestor-chain labels first diverge exactly identifies the depth at which their paths from the root separate — which is precisely the LCA's level.

**Why bit-tricks can locate the divergence in O(1):** Because each vertex's label is constructed so ancestor relationships correspond to specific bitwise prefix relationships (an ancestor's label is always a "bit-prefix" of any descendant's label under the encoding), computing the LCA reduces to finding the position of the most significant differing bit between two labels — an operation modern hardware performs in O(1) (e.g., via built-in bit-scan instructions), and then using this position to index directly into a precomputed jump structure that maps (vertex, target depth) to the correct ancestor in O(1), analogous to how binary lifting answers "jump to ancestor at depth `d`" but resolved without the O(log N) loop by encoding all needed jumps ahead of time.

**Correctness relies on:** (1) the DFS-order labeling correctly nesting subtree ranges (a standard, easily verified DFS property), and (2) the specific lowest/highest-bit propagation rule from Schieber & Vishkin's original 1988 paper correctly identifying divergence points — this second part is the technically intricate core of the algorithm and is proven rigorously in the original paper via careful induction on the bit-encoding invariants; a full derivation is beyond a short summary but the technique is well-established and widely implemented. ∎

### Variants / Use Cases
- **Binary lifting LCA** → simpler O(N log N) preprocessing, O(log N) query alternative, much easier to implement correctly
- **Sparse table + Euler tour RMQ-based LCA** → another simpler O(N log N) / O(1) alternative, more commonly used in competitive programming due to implementation simplicity
- **Real-time LCA queries at massive scale** → Schieber-Vishkin's true O(1) query (with only O(N) preprocessing and no log factor anywhere) matters in performance-critical systems processing huge query volumes
- **Static tree path queries** → any application needing extremely fast repeated ancestor queries on a fixed, large tree
- **Suffix tree / string algorithm internals** → LCA-heavy internal computations sometimes leverage the fastest available LCA structure
