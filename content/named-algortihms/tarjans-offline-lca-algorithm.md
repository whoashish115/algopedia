---
title: Tarjan's Offline LCA Algorithm
---

**Purpose:** Answer many Lowest Common Ancestor (LCA) queries on a tree offline, in O((N + Q)·α(N)) time using Union-Find.

### Algorithm
1. Group all queries by one of their two vertices, so each vertex knows which other vertices it needs an LCA answer with.
2. Initialize a Union-Find structure with each vertex in its own set, and an `ancestor[]` array marking the current Union-Find representative's actual tree ancestor.
3. Run a DFS from the root. When entering a vertex `u`, mark it visited.
4. Recursively process all of `u`'s children first (finish their entire subtree DFS), then after each child `c` finishes, union `c`'s set into `u`'s set and set `ancestor[find(u)] = u`.
5. After processing all children, for every query `(u, v)` where `v` has already been visited, the answer is `ancestor[find(v)]`.
6. Continue the DFS until all vertices are processed; all queries are now answered.

### Code
```cpp
vector<int> parent_, ancestor_;
vector<bool> visited;
vector<vector<int>> children, queryList; // queryList[u] = list of (v, queryIdx)
vector<int> lcaAnswer;

int find(int x) {
    while (parent_[x] != x) x = parent_[x] = parent_[parent_[x]];
    return x;
}

void unite(int x, int y) { parent_[find(x)] = find(y); }

void tarjanLCA(int u, vector<pair<int,int>>& queries, vector<vector<pair<int,int>>>& qAt) {
    visited[u] = true;
    ancestor_[find(u)] = u;

    for (int c : children[u]) {
        tarjanLCA(c, queries, qAt);
        unite(c, u);
        ancestor_[find(u)] = u;
    }

    for (auto& [v, idx] : qAt[u]) {
        if (visited[v]) lcaAnswer[idx] = ancestor_[find(v)];
    }
}
```

### Paradigm
**Transform and Conquer, built on Union-Find.** The algorithm transforms the problem of many independent LCA queries into a single DFS pass by exploiting the fact that once both nodes of a query have been visited, their LCA is exactly the tree ancestor tracked by the Union-Find structure — reducing repeated tree climbs to O(α(N)) amortized set operations.

### Complexity
- Time: O((N + Q) · α(N)), effectively linear
- Space: O(N + Q)

### Proof of Correctness
**Invariant:** At any point during the DFS, for any visited vertex `x`, `ancestor_[find(x)]` equals the *deepest* vertex on the current active root-to-current-node path that is an ancestor of `x` in the actual tree — which, once a vertex `u`'s subtree is fully processed, becomes `u` itself for every vertex inside that subtree.

**Why the query is answered correctly when both endpoints are visited:** Suppose query `(u, v)` is processed while at vertex `u`, and `v` was already visited earlier (meaning `v`'s entire subtree — or `v` is an ancestor of the current DFS path — has been fully explored before `u`'s post-processing). Since DFS visits vertices in a way that a vertex is only "visited" before its own subtree completes, and only fully completed subtrees get unioned into their parent, `find(v)`'s tracked ancestor is exactly the lowest fully-processed ancestor common to both `u`'s current position and `v`. Because DFS naturally ensures the LCA of `u` and `v` is exactly the last common ancestor still "open" (not yet fully finished and unioned upward) on the path from the root to both nodes, `ancestor_[find(v)]` at the moment `u` is being finished correctly evaluates to `LCA(u, v)`.

**Why processing order matters and why it's safe:** The union of `c` into `u` only happens *after* `c`'s entire subtree DFS completes, ensuring that queries answered from within `c`'s subtree (for vertices visited before `c` finished) never mistakenly resolve to `u` prematurely — the ancestor pointer update `ancestor_[find(u)] = u` only takes effect at the exact right time, guaranteeing that any query touching `u` and an already-visited vertex is resolved with the correct, currently valid LCA at that point in the traversal. ∎

### Variants / Use Cases
- **Binary lifting LCA** → online alternative (no need to batch queries in advance), O(log N) per query after O(N log N) preprocessing
- **Euler tour + sparse table LCA** → another online alternative achieving O(1) query after O(N log N) preprocessing
- **Range Minimum Query reduction** → LCA problems are commonly reduced to RMQ and solved via sparse tables or Cartesian trees
- **Network routing (common ancestor in hierarchical structures)** → LCA-style queries appear in organizational/hierarchical data lookups
- **Offline tree path queries (e.g. distance between two nodes)** → LCA is a building block: `dist(u,v) = depth[u] + depth[v] - 2·depth[LCA(u,v)]`