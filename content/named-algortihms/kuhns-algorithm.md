---
title: Kuhn's Algorithm
---

**Purpose:** Find a maximum matching in a bipartite graph, in O(VE) time, using one augmenting path per DFS.

### Algorithm
1. Maintain `matchR[]`, mapping each right vertex to its currently matched left vertex (or none).
2. For each left vertex, attempt to find an augmenting path via DFS: try each neighbor on the right.
3. If a neighbor is unmatched, match it to the current left vertex directly.
4. If a neighbor is already matched, recursively try to re-match its current partner to some other neighbor, freeing up this right vertex for the current left vertex.
5. If the DFS succeeds, the matching size increases by 1.
6. Repeat for every left vertex. The final matching is maximum.

### Code
```cpp
vector<int> matchR;
vector<bool> visited;
vector<vector<int>> adj;

bool tryKuhn(int u) {
    for (int v : adj[u]) {
        if (visited[v]) continue;
        visited[v] = true;
        if (matchR[v] == -1 || tryKuhn(matchR[v])) {
            matchR[v] = u;
            return true;
        }
    }
    return false;
}

int kuhnMaxMatching(int n, int m) {
    matchR.assign(m, -1);
    int result = 0;
    for (int u = 0; u < n; u++) {
        visited.assign(m, false);
        if (tryKuhn(u)) result++;
    }
    return result;
}
```

### Paradigm
**Greedy augmenting-path method.** It processes one left vertex at a time and commits to the first augmenting path found via DFS, relying on Berge's theorem to guarantee that repeatedly applying augmenting paths converges to a maximum matching.

### Complexity
- Time: O(VE)
- Space: O(V + E)

### Proof of Correctness
**Berge's theorem:** A matching `M` is maximum if and only if there is no augmenting path with respect to `M` (an alternating path of unmatched/matched edges that starts and ends at unmatched vertices).

**Why the DFS correctly finds an augmenting path if one exists:** Starting from an unmatched left vertex `u`, the DFS explores alternating paths: an unmatched edge to some right vertex `v`, then (if `v` is matched) a matched edge back to `v`'s partner, then continuing to search from there. This exactly traces the structure of an alternating path. If `v` is unmatched, the path ends here — this is a valid augmenting path. The `visited[]` array prevents revisiting a right vertex within the same search, which is safe because reusing a right vertex would not correspond to a simple augmenting path.

**Why flipping matched/unmatched edges along the path increases matching size:** An augmenting path has one more unmatched edge than matched edges. Swapping the roles (matched becomes unmatched and vice versa) along the path keeps every vertex on the path matched except it converts one previously-unmatched endpoint into matched — increasing total matched pairs by exactly 1, and validity (no vertex matched twice) is preserved since the path only touches each vertex once.

**Why processing all left vertices gives a maximum matching:** After no more augmenting paths exist from any unmatched left vertex, by Berge's theorem the matching is maximum, since an augmenting path for the *whole* graph must start at some unmatched left vertex (bipartite structure), and every candidate was already tried and failed to find one. ∎

### Variants / Use Cases
- **Hopcroft-Karp** → phase-based version finding multiple disjoint augmenting paths per round, faster at O(E√V)
- **Hungarian Algorithm** → weighted generalization (minimum-cost perfect matching)
- **König's theorem application** → maximum matching size equals minimum vertex cover size in bipartite graphs, derivable from the final matching structure
- **Stable marriage-adjacent problems** → related bipartite pairing problems, though solved with different algorithms (Gale-Shapley)
- **Resource allocation, scheduling** → any bipartite "who can do what" assignment problem