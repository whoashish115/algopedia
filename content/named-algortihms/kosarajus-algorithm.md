---
title: Kosaraju's Algorithm
---

**Purpose:** Find all Strongly Connected Components (SCCs) in a directed graph, in O(V + E) time using two passes of DFS.
### Algorithm
1. Run a DFS on the original graph, and whenever a vertex finishes (all its descendants are processed), push it onto a stack.
2. Reverse every edge in the graph to build the transpose graph.
3. Pop vertices off the stack one by one (highest finish time first). For each unvisited vertex popped, run a DFS on the transpose graph from it.
4. Every vertex reached in this DFS belongs to the same SCC as the starting vertex.
5. Repeat until the stack is empty. Each DFS tree from step 3 is exactly one SCC.

### Code

```cpp
void dfs1(int u, vector<vector<int>>& adj, vector<bool>& visited, stack<int>& order) {
    visited[u] = true;
    for (int v : adj[u]) if (!visited[v]) dfs1(v, adj, visited, order);
    order.push(u);
}

void dfs2(int u, vector<vector<int>>& radj, vector<bool>& visited, vector<int>& component) {
    visited[u] = true;
    component.push_back(u);
    for (int v : radj[u]) if (!visited[v]) dfs2(v, radj, visited, component);
}

vector<vector<int>> kosaraju(int n, vector<vector<int>>& adj) {
    vector<bool> visited(n, false);
    stack<int> order;
    for (int i = 0; i < n; i++) if (!visited[i]) dfs1(i, adj, visited, order);

    vector<vector<int>> radj(n);
    for (int u = 0; u < n; u++)
        for (int v : adj[u]) radj[v].push_back(u);

    fill(visited.begin(), visited.end(), false);
    vector<vector<int>> sccs;
    while (!order.empty()) {
        int u = order.top(); order.pop();
        if (!visited[u]) {
            vector<int> component;
            dfs2(u, radj, visited, component);
            sccs.push_back(component);
        }
    }
    return sccs;
}
```

### Paradigm
**Transform and Conquer.** The core trick is instance simplification: reordering vertices by DFS finish time and transforming the graph into its transpose reduces the hard problem of finding SCCs directly into two straightforward DFS traversals.

### Complexity
- Time: O(V + E)
- Space: O(V + E)

### Proof of Correctness
**Key Lemma:** If there is an edge from SCC `A` to SCC `B` (A ≠ B) in the original graph, then the maximum finish time (from the first DFS) among vertices in `A` is greater than the maximum finish time among vertices in `B`.

_Proof of lemma:_ Consider whichever of `A` or `B` the first DFS discovers first.
- If `A` is discovered first: since an edge leads from `A` into `B`, the DFS exploring `A` will descend into `B` before backtracking (no edge exists from `B` back to `A`, or they'd be one SCC), so all of `B` finishes before the entry vertex of `A` finishes — giving `A` the higher max finish time.
- If `B` is discovered first: since there's no edge from `B` to `A`, DFS starting in `B` cannot reach `A`, so all of `B` finishes completely before any vertex of `A` is even discovered — again giving `A` the higher max finish time.

**Main proof:** In the second pass, DFS runs on the transpose graph in decreasing order of finish time. Let `u` be the first unvisited vertex processed, belonging to SCC `C`, which has the highest remaining finish time. By the lemma (applied to the original graph), no other unvisited SCC `D` can have an edge into `C` in the original graph — otherwise `D` would need a higher finish time than `C`, contradicting `C`'s selection. This means no edge from `C` to an unvisited SCC exists in the _transpose_ graph either. So the DFS from `u` on the transpose graph cannot escape `C`, and since `C` is strongly connected, it reaches every vertex in `C` — no more, no less. Repeating this argument for each subsequent pop correctly peels off one SCC at a time. ∎

### Variants / Use Cases
- **Tarjan's Algorithm** → alternative single-pass DFS approach using low-link values instead of two full passes
- **Condensation graph construction** → contract each SCC into a single node to get a DAG, useful for further DAG-based processing
- **2-SAT solving** → build an implication graph and use SCCs to check satisfiability and extract a valid assignment
- **Dead code / unreachable state elimination** → SCCs identify cyclic dependency groups in compilers and state machines
- **Web page/community detection** → SCCs in web graphs or social graphs reveal mutually-reachable clusters