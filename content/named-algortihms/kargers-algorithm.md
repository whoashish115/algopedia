---
title: Karger's Algorithm
---

**Purpose:** Find the global minimum cut of an undirected graph using randomized edge contraction, in O(E) time per trial (O(V² · E) total with enough repetitions for high success probability).

### Algorithm
1. While more than 2 vertices remain in the (multi-)graph:
2. Pick a remaining edge uniformly at random.
3. Contract this edge: merge its two endpoints into a single vertex, removing self-loops but keeping parallel edges (multi-edges) to other vertices.
4. Repeat until only 2 "super-vertices" remain.
5. The edges remaining between these two super-vertices form a candidate cut; its size is a candidate minimum cut value.
6. Repeat the entire process many times (with fresh random choices) and take the smallest cut found across all trials.

### Code
```cpp
int kargerMinCut(int n, vector<pair<int,int>> edges) {
    vector<int> parent(n);
    iota(parent.begin(), parent.end(), 0);

    function<int(int)> find = [&](int x) {
        while (parent[x] != x) x = parent[x] = parent[parent[x]];
        return x;
    };

    int verticesLeft = n;
    mt19937 rng(random_device{}());

    while (verticesLeft > 2) {
        int idx = rng() % edges.size();
        int u = find(edges[idx].first), v = find(edges[idx].second);
        if (u == v) { edges.erase(edges.begin() + idx); continue; }

        parent[u] = v;
        verticesLeft--;
        edges.erase(edges.begin() + idx);
    }

    int cutSize = 0;
    for (auto& [u, v] : edges) if (find(u) != find(v)) cutSize++;
    return cutSize;
}

int kargerRepeated(int n, vector<pair<int,int>>& edges, int trials) {
    int best = INT_MAX;
    for (int i = 0; i < trials; i++) best = min(best, kargerMinCut(n, edges));
    return best;
}
```

### Paradigm
**Randomized.** Each trial makes a sequence of uniformly random contraction choices; correctness is not guaranteed on any single run but holds with a provable success probability, so the algorithm is repeated to amplify confidence — the defining structure of a randomized (Monte Carlo) algorithm.

### Complexity
- Time: O(E) per trial; O(V²) trials needed for high success probability, giving O(V² · E) total (O(V⁴) on dense graphs, improvable with Karger-Stein to O(V² log³V))
- Space: O(V + E)

### Proof of Correctness
**Single-trial success probability:** Fix some particular global minimum cut `C` of size `k`. A single trial fails to find (an cut at least as good as) `C` only if it happens to contract an edge that crosses `C` before the process finishes. Since the graph has minimum degree ≥ `k` (as `k` is the min cut, every vertex's degree is at least `k`, otherwise a smaller cut around that single vertex would exist), the total edge count when `n'` vertices remain is at least `k·n'/2`, so a uniformly random edge crosses `C` with probability at most `k / (k·n'/2) = 2/n'`. The probability that contraction survives *all* steps without hitting `C` is:
```
Π (from n'=n down to 3) of (1 - 2/n') = Π (n'-2)/n' = [2 / (n(n-1))]
```
which telescopes to exactly `2 / (n(n-1))` — i.e., a single trial succeeds in preserving cut `C` with probability at least `2/n²` (using ≥ since other cuts could also coincidentally be found).

**Why repeated trials boost success probability arbitrarily high:** Running `T` independent trials, the probability that *all* fail is at most `(1 - 2/n²)^T`. Choosing `T = O(n² log n)` makes this failure probability polynomially small (less than any inverse polynomial in `n`), so with high probability, at least one trial finds the true global minimum cut.

**Why the reported answer is always a valid upper bound:** Every trial always ends with a genuine cut of the graph (the edges between the final two super-vertices always disconnect the original graph into two non-empty groups by construction), so the algorithm's output is never smaller than the true minimum cut — only correctness of finding the *exact* minimum requires the probability argument above. ∎

### Variants / Use Cases
- **Karger-Stein algorithm** → recursively branches near the end of contraction (where failure probability is highest) to improve total time to O(V² log³V)
- **Stoer-Wagner Algorithm** → deterministic alternative for global min-cut, no repetition needed
- **Network reliability estimation** → randomized min-cut sampling used to estimate network robustness
- **Graph partitioning heuristics** → contraction-based ideas appear broadly in coarsening approaches (e.g., in METIS-style partitioners)
- **Clustering via repeated min-cut** → randomized cuts can reveal natural graph community structure across many trials
