---
title: Hopcroft-Karp Algorithm
---

**Purpose:** Find a maximum matching in a bipartite graph, in O(E√V) time, by augmenting along multiple shortest augmenting paths per phase.
### Algorithm
1. Maintain a matching (initially empty), represented by `matchL[]` and `matchR[]` pointing matched partners across the two sides.
2. In each phase, run a BFS from all unmatched left vertices simultaneously to compute layered distances along alternating (unmatched-edge, matched-edge) paths, stopping at the first layer that reaches an unmatched right vertex.
3. If no unmatched right vertex is reached, stop — the matching is already maximum.
4. Otherwise, run a DFS from each unmatched left vertex, only following edges that respect the layered distances computed in the BFS, to find vertex-disjoint shortest augmenting paths.
5. Augment the matching along every such path found (flip matched/unmatched edges along the path).
6. Repeat from step 2.

### Code
```cpp
struct HopcroftKarp {
    int n, m;
    vector<vector<int>> adj;
    vector<int> matchL, matchR, dist;
    const int NIL = 0, INF = INT_MAX;

    HopcroftKarp(int n, int m) : n(n), m(m), adj(n + 1), matchL(n + 1, NIL), matchR(m + 1, NIL), dist(n + 1) {}

    bool bfs() {
        queue<int> q;
        for (int u = 1; u <= n; u++) {
            if (matchL[u] == NIL) { dist[u] = 0; q.push(u); }
            else dist[u] = INF;
        }
        bool found = false;
        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : adj[u]) {
                int w = matchR[v];
                if (w == NIL) found = true;
                else if (dist[w] == INF) { dist[w] = dist[u] + 1; q.push(w); }
            }
        }
        return found;
    }

    bool dfs(int u) {
        for (int v : adj[u]) {
            int w = matchR[v];
            if (w == NIL || (dist[w] == dist[u] + 1 && dfs(w))) {
                matchL[u] = v; matchR[v] = u;
                return true;
            }
        }
        dist[u] = INF;
        return false;
    }

    int maxMatching() {
        int result = 0;
        while (bfs()) {
            for (int u = 1; u <= n; u++)
                if (matchL[u] == NIL && dfs(u)) result++;
        }
        return result;
    }
};
```

### Paradigm
**Greedy (phase-based, augmenting-path method).** Each phase greedily augments along a maximal set of vertex-disjoint shortest augmenting paths and never revisits that phase's decisions, similar in spirit to Dinic's level-graph blocking-flow approach.

### Complexity
- Time: O(E√V)
- Space: O(V + E)

### Proof of Correctness
**Correctness of augmenting via shortest augmenting paths:** By Berge's theorem, a matching is maximum if and only if it has no augmenting path. Each phase's BFS+DFS finds a maximal set of vertex-disjoint *shortest* augmenting paths and applies all of them, which by standard augmenting-path theory strictly increases the matching size and never decreases the shortest augmenting path length found in the next phase.

**Why the shortest augmenting path length strictly increases each phase:** After augmenting along all shortest paths of a given length in one phase, any remaining augmenting path must be strictly longer, because the shortest ones have been used up and the layered BFS structure guarantees no shorter one can reappear using the same or lower distance labels — analogous to the level-graph argument in Dinic's algorithm.

**Why O(√V) phases suffice:** If the maximum matching has size `M` and the current matching is short by `k` augmenting paths, standard matching theory shows there must exist at least `k` vertex-disjoint augmenting paths, and by a counting argument once the shortest augmenting path length exceeds `√V`, at most `√V` augmenting paths can remain (since disjoint paths of length `> √V` each use `> √V` vertices, but only `V` vertices exist). So after O(√V) phases of growing path length, the algorithm converges. Each phase costs O(E) for BFS+DFS, giving O(E√V) total. ∎

### Variants / Use Cases
- **Kuhn's Algorithm** → simpler O(VE) bipartite matching using one augmenting path per DFS, without phase batching
- **Hungarian Algorithm** → weighted bipartite matching (assignment problem) extension
- **Dinic's Algorithm on unit-capacity graphs** → structurally equivalent phase-based idea applied to general flow
- **Job assignment / task scheduling** → classic real-world bipartite matching application
- **Online bipartite matching** → related but must handle vertices arriving over time, different techniques apply