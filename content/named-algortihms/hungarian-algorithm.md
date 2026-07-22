---
title: Hungarian Algorithm
---


**Purpose:** Solve the assignment problem — find a minimum-cost perfect matching in a weighted bipartite graph — in O(V³) time.

### Algorithm
1. Represent the problem as an `n x n` cost matrix, where `cost[i][j]` is the cost of assigning left-vertex `i` to right-vertex `j`.
2. Maintain potentials (dual variables) `u[]` for left vertices and `v[]` for right vertices, and a partial matching.
3. For each left vertex, find an augmenting path to an unmatched right vertex using only "tight" edges (edges where `cost[i][j] = u[i] + v[j]`), via a Dijkstra-like shortest-path search.
4. If no tight augmenting path exists, update potentials by the smallest slack found, which makes at least one new edge tight without breaking optimality, and retry.
5. Once an augmenting path reaches an unmatched right vertex, augment the matching along it.
6. Repeat until every left vertex is matched. The total matching cost is the minimum possible.

### Code
```cpp
// O(n^3) Hungarian algorithm (Jonker-Volgenant style), 1-indexed cost matrix
int hungarian(int n, vector<vector<int>>& cost) {
    const int INF = INT_MAX;
    vector<int> u(n + 1), v(n + 1), p(n + 1), way(n + 1);

    for (int i = 1; i <= n; i++) {
        p[0] = i;
        int j0 = 0;
        vector<int> minv(n + 1, INF);
        vector<bool> used(n + 1, false);

        do {
            used[j0] = true;
            int i0 = p[j0], delta = INF, j1 = -1;
            for (int j = 1; j <= n; j++) {
                if (!used[j]) {
                    int cur = cost[i0][j] - u[i0] - v[j];
                    if (cur < minv[j]) { minv[j] = cur; way[j] = j0; }
                    if (minv[j] < delta) { delta = minv[j]; j1 = j; }
                }
            }
            for (int j = 0; j <= n; j++) {
                if (used[j]) { u[p[j]] += delta; v[j] -= delta; }
                else minv[j] -= delta;
            }
            j0 = j1;
        } while (p[j0] != 0);

        do {
            int j1 = way[j0];
            p[j0] = p[j1];
            j0 = j1;
        } while (j0);
    }

    int result = 0;
    for (int j = 1; j <= n; j++) result += cost[p[j]][j];
    return result;
}
```

### Paradigm
**Transform and Conquer, built on Greedy shortest-path search.** The core idea is a primal-dual transform: potentials `u[], v[]` reformulate the problem into finding augmenting paths through a "tight-edge" subgraph, turning the assignment problem into repeated shortest-path (greedy, Dijkstra-like) searches.

### Complexity
- Time: O(V³)
- Space: O(V²)

### Proof of Correctness
**Duality invariant:** At every stage, the algorithm maintains `u[i] + v[j] ≤ cost[i][j]` for all `i, j` (feasibility of the dual), with equality (tightness) along matched edges. This is a certificate: for any matching `M` respecting only tight edges, its cost equals `Σ u[i] + Σ v[j]`, and for any feasible dual, this sum lower-bounds the cost of *every* perfect matching (since `cost[i][j] ≥ u[i] + v[j]` for all edges, summing over any perfect matching's edges gives total cost ≥ `Σu + Σv`).

**Why potential updates preserve feasibility:** When no augmenting path exists using only tight edges, the algorithm raises `u[i]` for visited left vertices and lowers `v[j]` for visited right vertices by the minimum slack `delta` among non-tight edges leaving the visited set. This keeps every edge's slack `cost[i][j] - u[i] - v[j] ≥ 0` (edges inside the visited set are unaffected since both endpoints shift equally; edges leaving the visited set have their slack reduced by exactly `delta`, chosen as the minimum so none go negative), and it makes at least one new edge tight, guaranteeing progress.

**Optimality at termination:** When all `n` left vertices are matched using only tight edges, the matching cost equals `Σu[i] + Σv[j]`, which by the lower-bound argument above equals the minimum possible cost over any perfect matching — so the matching found is optimal. ∎

### Variants / Use Cases
- **Rectangular assignment problem** → extended for non-square cost matrices via dummy rows/columns
- **Maximum weight bipartite matching** → negate costs to convert min-cost to max-weight
- **Kuhn-Munkres in O(n³) vs naive O(n⁴)** → the Jonker-Volgenant style shown here achieves the tighter bound
- **Task/resource assignment (job scheduling, ad allocation)** → classic real-world use
- **Transportation problem** → generalization allowing unequal supply/demand at each vertex
