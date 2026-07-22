---
title: Karp's Minimum Mean Cycle Algorithm
---

**Purpose:** Find the cycle in a directed weighted graph with the minimum average edge weight (total weight divided by number of edges), in O(VE) time.

### Algorithm
1. Pick an arbitrary source vertex `s` and define `d[k][v]` as the minimum total weight of a walk of exactly `k` edges from `s` to `v` (using dynamic programming over walk length, allowing revisits).
2. Compute `d[k][v]` for all `k` from 0 to `V`, using the recurrence: `d[k][v] = min over edges (u,v) of d[k-1][u] + weight(u,v)`.
3. For every vertex `v`, compute `max over k from 0 to V-1 of (d[V][v] - d[k][v]) / (V - k)`.
4. Take the minimum of this value over all vertices `v` — this equals the minimum mean cycle weight.
5. (To recover the actual cycle, backtrack through the `d[][]` table along the achieving path.)

### Code
```cpp
double minMeanCycle(int n, vector<array<int,3>>& edges, int src) {
    const double INF = 1e18;
    vector<vector<double>> d(n + 1, vector<double>(n, INF));
    d[0][src] = 0;

    for (int k = 1; k <= n; k++) {
        for (auto& [u, v, w] : edges) {
            if (d[k-1][u] < INF) {
                d[k][v] = min(d[k][v], d[k-1][u] + w);
            }
        }
    }

    double result = INF;
    for (int v = 0; v < n; v++) {
        if (d[n][v] >= INF) continue;
        double worst = -INF;
        for (int k = 0; k < n; k++) {
            if (d[k][v] < INF) {
                worst = max(worst, (d[n][v] - d[k][v]) / (n - k));
            }
        }
        result = min(result, worst);
    }
    return result;
}
```

### Paradigm
**Dynamic Programming.** `d[k][v]` is the optimal solution to the subproblem "cheapest walk of exactly `k` edges ending at `v`," built directly from the `d[k-1][*]` layer, and the minimum mean cycle is extracted from this DP table via Karp's closed-form formula.

### Complexity
- Time: O(VE)
- Space: O(V²)

### Proof of Correctness
**Why the DP layer `d[k][v]` is well-defined and correct:** By construction, `d[k][v]` is computed as the minimum over all valid length-`(k-1)` walks extended by one edge, which by induction on `k` correctly represents the minimum-weight walk of exactly `k` edges from `s` to `v` (base case `d[0][s] = 0` trivially correct; inductive step directly follows the recurrence, mirroring Bellman-Ford's layered relaxation).

**Why Karp's formula extracts the minimum mean cycle:** The key insight is that for any vertex `v` reachable via a walk that closes into the minimum mean cycle, the quantity `(d[n][v] - d[k][v]) / (n - k)`, maximized over `k`, converges to exactly the minimum mean cycle weight when `v` lies on (or connects into) that cycle. This follows from a theorem relating the growth rate of shortest walk weights to cycle means: for large walk lengths, `d[k][v]/k` approaches the minimum mean cycle weight reachable from `s`, and the specific max-over-k formula precisely isolates this asymptotic rate without needing arbitrarily long walks, by algebraically capturing the "slope" between any two points on the walk-weight-vs-length curve. A full proof (Karp, 1978) shows this maximum is tight exactly at the minimum mean cycle and never underestimates it for any graph structure.

**Why taking the minimum over all vertices works:** Since every cycle is reachable from *some* vertex on it, and the source `s` is assumed to reach all relevant vertices (or the graph is preprocessed to ensure reachability), scanning all `v` guarantees the true minimum mean cycle is found regardless of which vertex it happens to pass through first. ∎

### Variants / Use Cases
- **Minimum mean cost cycle canceling (min-cost flow)** → repeatedly finding and canceling minimum mean cycles is a classic min-cost flow algorithm with strongly polynomial time
- **Maximum mean cycle** → negate all weights and apply the same algorithm
- **Detecting arbitrage-free markets** → minimum mean cycle in log-transformed exchange rate graphs detects the most profitable arbitrage rate
- **Scheduling with cyclic constraints (timing analysis)** → minimum/maximum mean cycle bounds are used in digital circuit and pipeline timing verification
- **Karp-Orlin algorithm** → related technique for parametric shortest paths building on similar mean-weight ideas
