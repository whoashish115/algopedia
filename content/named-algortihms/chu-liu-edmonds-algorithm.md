---
title: Chu-Liu-Edmonds' Algorithm
---

**Purpose:** Find a Minimum (or Maximum) Spanning Arborescence — a directed spanning tree rooted at a given vertex with minimum total edge weight — in O(VE) time (O(E log V) with optimized data structures).

### Algorithm
1. For every vertex except the root, select its cheapest incoming edge.
2. If these selected edges form no cycle, they directly form the minimum arborescence — done.
3. If a cycle exists among the selected edges, contract it into a single super-vertex.
4. Reweight every edge entering the cycle: subtract the weight of the cycle's edge that would have been replaced at the entry vertex, so entering the contracted node reflects the true added cost.
5. Recurse on this smaller contracted graph to find its minimum arborescence.
6. Expand the contracted super-vertex back: break exactly one edge in the original cycle (the one corresponding to whichever edge enters the cycle in the recursive solution) to restore a valid tree structure.

### Code
```cpp
// Simplified O(VE) Chu-Liu/Edmonds' implementation
struct Edge { int u, v, w; };

int minArborescence(int n, int root, vector<Edge> edges) {
    int result = 0;
    while (true) {
        vector<int> inEdge(n, -1), inWeight(n, INT_MAX);
        for (auto& e : edges) {
            if (e.u != e.v && e.w < inWeight[e.v]) { inWeight[e.v] = e.w; inEdge[e.v] = e.u; }
        }
        for (int v = 0; v < n; v++) if (v != root && inEdge[v] == -1) return -1; // unreachable

        vector<int> id(n, -1), cycleId(n, -1);
        int cycleCount = 0;
        for (int v = 0; v < n; v++) {
            if (v == root) continue;
            result += inWeight[v];
            int u = v;
            vector<int> path;
            while (u != root && id[u] == -1 && find(path.begin(), path.end(), u) == path.end()) {
                path.push_back(u);
                u = inEdge[u];
            }
            if (u != root && id[u] == -1) {
                for (int x : path) { id[x] = cycleCount; }
                cycleCount++;
            }
        }
        if (cycleCount == 0) return result;

        for (int v = 0; v < n; v++) if (id[v] == -1) id[v] = cycleCount++;

        vector<Edge> newEdges;
        for (auto& e : edges) {
            int u = id[e.u], v = id[e.v];
            if (u != v) newEdges.push_back({u, v, (id[e.v] < cycleCount && inEdge[e.v] != -1) ? e.w - inWeight[e.v] : e.w});
        }
        edges = newEdges;
        n = cycleCount;
        root = id[root];
    }
}
```

### Paradigm
**Greedy combined with Transform and Conquer.** Each round greedily picks the cheapest incoming edge per vertex; when this creates a cycle, the cycle is contracted (an instance-simplifying transform) and the same greedy step is reapplied recursively on the smaller graph.

### Complexity
- Time: O(VE) in the simple version; O(E log V) with efficient priority-queue-based edge selection
- Space: O(V + E)

### Proof of Correctness
**Why picking each vertex's cheapest incoming edge is safe (when acyclic):** In any arborescence rooted at `root`, every non-root vertex has exactly one incoming edge. Choosing the globally cheapest available incoming edge for each vertex independently can only be suboptimal if it's somehow forced to conflict with another vertex's optimal choice — but since incoming edges for different vertices are chosen independently and a valid arborescence has one per vertex anyway, if no cycle forms, this greedy selection is trivially optimal (nothing cheaper could ever be used).

**Why cycle contraction preserves optimality:** If the cheapest-incoming-edge selection creates a cycle `C`, any valid arborescence must "break into" `C` from outside via exactly one edge (since the root is external to `C`, some edge must enter it, and having two would create an extra cycle/non-tree structure). For a candidate external edge `(x, v)` entering the cycle at vertex `v`, the true cost of choosing this entry (replacing `v`'s original cheap in-cycle edge) is `w(x,v) - inWeight[v] + (cost to complete the rest of the cycle via its own cheap edges)`, since accepting `(x,v)` forces dropping exactly the one edge inside the cycle that used to feed `v`, while every other cycle vertex still gets its cheap in-cycle edge for free. Subtracting `inWeight[v]` from `w(x,v)` for edges entering the cycle exactly captures this trade-off, so solving the contracted problem correctly captures the true cost of every way to break into the cycle.

**Termination:** Each contraction strictly reduces the number of vertices (a cycle of length ≥ 2 collapses to 1), so recursion depth is bounded by `V`, and each level costs O(E) to scan all edges — giving O(VE) total. ∎

### Variants / Use Cases
- **Maximum spanning arborescence** → negate weights and apply the same algorithm
- **Optimal branchings in dependency graphs** → real-world use in compiler/task scheduling with directed prerequisite structures
- **Network design (directed broadcast trees)** → minimum-cost way to reach every node from a source in a directed network
- **Root selection variants** → run once per candidate root to find the overall best-rooted arborescence
- **Tarjan's O(E log V) optimized implementation** → uses efficient heaps for cheapest-edge selection and union-find for cycle detection
