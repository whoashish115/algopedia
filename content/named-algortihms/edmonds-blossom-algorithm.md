---
title: Edmonds' Blossom Algorithm
---

**Purpose:** Find a maximum matching in a general (non-bipartite) graph, in O(V³) time, by handling odd-length cycles ("blossoms") that break simple bipartite augmenting-path search.

### Algorithm
1. Maintain a matching (initially empty) and repeat: search for an augmenting path from each unmatched vertex via alternating BFS/DFS, just like bipartite matching.
2. While building the alternating tree, if the search ever finds an edge connecting two vertices at the same "even" depth in the tree (both reachable via an even number of matched-edge steps from the root), this indicates an odd cycle — a **blossom** — has formed.
3. Contract the entire blossom into a single super-vertex, treating it as one node in the graph for the rest of this search.
4. Continue the augmenting-path search on the contracted graph as usual.
5. If an augmenting path is found (possibly passing through the contracted blossom), "lift" the path back through the blossom: unwind the contraction, choosing the correct direction around the (odd) blossom cycle so the path remains a valid alternating path.
6. Augment the matching along this lifted path. Repeat until no augmenting path exists from any unmatched vertex.

### Code
```cpp
// Simplified blossom algorithm skeleton (O(V^3)) — full implementations are lengthy;
// this shows the core structure: BFS with blossom contraction and path lifting.
struct Blossom {
    int n;
    vector<vector<int>> adj;
    vector<int> match, p, base;
    vector<bool> used, blossomFlag;

    Blossom(int n) : n(n), adj(n), match(n, -1), p(n), base(n), used(n), blossomFlag(n) {}

    void addEdge(int u, int v) { adj[u].push_back(v); adj[v].push_back(u); }

    int lca(int a, int b, vector<int>& parent) {
        vector<bool> seen(n, false);
        int x = a;
        while (true) { x = base[x]; seen[x] = true; if (match[x] == -1) break; x = p[match[x]]; }
        int y = b;
        while (true) { y = base[y]; if (seen[y]) return y; y = p[match[y]]; }
    }

    void markPath(int v, int b, int child, queue<int>& q) {
        while (base[v] != b) {
            blossomFlag[base[v]] = true;
            blossomFlag[base[match[v]]] = true;
            p[v] = child;
            child = match[v];
            v = p[match[v]];
        }
    }

    int findPath(int root) {
        fill(used.begin(), used.end(), false);
        fill(p.begin(), p.end(), -1);
        for (int i = 0; i < n; i++) base[i] = i;

        used[root] = true;
        queue<int> q; q.push(root);
        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : adj[u]) {
                if (base[u] == base[v] || match[u] == v) continue;
                if (v == root || (match[v] != -1 && p[match[v]] != -1)) {
                    int curBase = lca(u, v, p);
                    fill(blossomFlag.begin(), blossomFlag.end(), false);
                    markPath(u, curBase, v, q);
                    markPath(v, curBase, u, q);
                    for (int i = 0; i < n; i++) {
                        if (blossomFlag[base[i]]) {
                            base[i] = curBase;
                            if (!used[i]) { used[i] = true; q.push(i); }
                        }
                    }
                } else if (p[v] == -1) {
                    p[v] = u;
                    if (match[v] == -1) return v;
                    used[match[v]] = true;
                    q.push(match[v]);
                }
            }
        }
        return -1;
    }

    int maxMatching() {
        int result = 0;
        for (int v = 0; v < n; v++) {
            if (match[v] == -1) {
                int u = findPath(v);
                if (u != -1) {
                    result++;
                    while (u != -1) {
                        int pv = p[u], ppv = match[pv];
                        match[u] = pv; match[pv] = u;
                        u = ppv;
                    }
                }
            }
        }
        return result;
    }
};
```

### Paradigm
**Transform and Conquer.** The blossom-contraction step is an instance simplification: it collapses an odd cycle that would otherwise break the bipartite-style augmenting-path search into a single super-vertex, transforming a hard general-graph instance into an easier one solvable with standard alternating-path search.

### Complexity
- Time: O(V³)
- Space: O(V²)

### Proof of Correctness
**Why odd cycles break naive bipartite search:** In bipartite graphs, alternating paths never revisit the same "side," so BFS/DFS depth parity is consistent. In general graphs, an odd cycle can create an edge between two vertices at the same even depth, which the naive algorithm would misinterpret, potentially missing a valid augmenting path that actually threads through the cycle.

**Why contraction preserves augmenting paths:** A blossom is an odd-length alternating cycle reachable from the root by an even-length alternating path. Any augmenting path entering the blossom can always be rerouted to traverse an even number of edges around the odd cycle before exiting toward the unmatched vertex outside it (since one direction around an odd cycle from any entry point has even length and the other odd, and matched/unmatched edges alternate consistently in at least one of these directions). This means treating the whole blossom as a single vertex doesn't lose any augmenting path — if one exists in the original graph, a corresponding one exists in the contracted graph, and vice versa (by the lifting procedure, which correctly reconstructs the alternating path around the cycle).

**Why the algorithm terminates at a maximum matching:** By the same Berge's theorem used in bipartite matching (a matching is maximum iff it has no augmenting path), and since blossom contraction doesn't lose any augmenting path's existence, exhausting all searches without finding one confirms maximality. ∎

### Variants / Use Cases
- **General graph maximum matching** → the defining use case; bipartite algorithms (Kuhn's, Hopcroft-Karp) don't handle odd cycles
- **Chinese Postman Problem on general graphs** → requires general matching to pair up odd-degree vertices optimally
- **Weighted general matching (Edmonds' algorithm for weighted case)** → extension combining blossom shrinking with primal-dual weight updates
- **T-joins and general graph optimization** → blossom-based techniques appear broadly in combinatorial optimization
- **Micali-Vazirani algorithm** → faster O(E√V) general matching, a more intricate descendant of blossom contraction