---
title: Tarjan's Algorithm
---

**Purpose:** Find all Strongly Connected Components (SCCs) in a directed graph, in O(V + E) time using a single DFS pass with low-link values.

### Algorithm
1. Do a DFS from any unvisited vertex. Assign each vertex a `disc` value (discovery time/order visited) and a `low` value (initially same as `disc`).
2. Push every visited vertex onto a stack and mark it as "on stack."
3. For each neighbor: if unvisited, recurse into it, then update the current vertex's `low` to the minimum of its own `low` and the neighbor's `low` (this propagates how far back up the tree a descendant can reach). If the neighbor is already visited and on the stack, update `low` to the minimum of current `low` and the neighbor's `disc`.
4. After visiting all neighbors of a vertex, check if `disc == low` for that vertex — this means it's the "root" of an SCC.
5. If it's a root, pop vertices off the stack until (and including) this vertex — all popped vertices form one SCC.
6. Repeat for all unvisited vertices.

### Code

```cpp
void tarjanDFS(int u, vector<vector<int>>& adj, vector<int>& disc, vector<int>& low,
               vector<bool>& onStack, stack<int>& st, int& timer, vector<vector<int>>& sccs) {
    disc[u] = low[u] = timer++;
    st.push(u);
    onStack[u] = true;

    for (int v : adj[u]) {
        if (disc[v] == -1) {
            tarjanDFS(v, adj, disc, low, onStack, st, timer, sccs);
            low[u] = min(low[u], low[v]);
        } else if (onStack[v]) {
            low[u] = min(low[u], disc[v]);
        }
    }

    if (disc[u] == low[u]) {
        vector<int> component;
        while (st.top() != u) {
            component.push_back(st.top());
            onStack[st.top()] = false;
            st.pop();
        }
        component.push_back(st.top());
        onStack[st.top()] = false;
        st.pop();
        sccs.push_back(component);
    }
}

vector<vector<int>> tarjanSCC(int n, vector<vector<int>>& adj) {
    vector<int> disc(n, -1), low(n, -1);
    vector<bool> onStack(n, false);
    stack<int> st;
    int timer = 0;
    vector<vector<int>> sccs;

    for (int i = 0; i < n; i++) {
        if (disc[i] == -1) tarjanDFS(i, adj, disc, low, onStack, st, timer, sccs);
    }
    return sccs;
}
```

### Paradigm
**Decrease and Conquer.** A single DFS incrementally builds up `low` values as it unwinds the recursion, peeling off one SCC at a time whenever a root is found — reducing the problem to smaller unresolved subgraphs without needing a separate transform or second pass.

### Complexity
- Time: O(V + E)
- Space: O(V)

### Proof of Correctness
**Key invariant:** `low[u]` equals the smallest `disc` value reachable from `u` by using tree edges downward and at most one back/cross edge to a vertex still on the stack.

**Why `disc[u] == low[u]` identifies an SCC root:** If `low[u] == disc[u]`, it means no descendant of `u` in the DFS tree has a back edge reaching any ancestor of `u` (or `u` itself would have inherited a smaller `low` value). This means `u` and all its descendants still on the stack cannot escape upward past `u` — they form a "closed" group where `u` is the earliest-discovered common ancestor reachable by all of them, and by construction, each of them can also reach back to `u` (since they're on the stack, meaning still part of an active, unresolved component connected via back edges). This mutual reachability is exactly the definition of an SCC.

**Why vertices below `u` on the stack (when found) form exactly that SCC:** The "on stack" property tracks vertices that are part of some not-yet-finalized SCC. Any vertex popped before reaching `u` must have had a path back to `u` (otherwise its own `low` would have equaled its own `disc`, and it would have been popped as its own SCC root earlier). So every vertex between the top of the stack and `u` is mutually reachable with `u`, confirming they belong together in one SCC.

**No vertex is placed in two SCCs:** Once a vertex is popped and marked off-stack, it can never be revisited or included in a later SCC, since the algorithm only considers on-stack vertices when updating `low`. This guarantees SCCs found are disjoint and every vertex belongs to exactly one. ∎

### Variants / Use Cases
- **Kosaraju's Algorithm** → alternative two-pass DFS approach; Tarjan's is often preferred for being single-pass and slightly more efficient in practice
- **Articulation points / bridges** → same low-link technique adapted to find cut vertices and cut edges in undirected graphs
- **Biconnected components** → extension of the low-link idea to undirected graphs using a similar stack-based peeling process
- **2-SAT solving** → build an implication graph and use SCCs to check satisfiability and extract a valid assignment
- **Condensation graph construction** → contract each SCC into a single node to get a DAG for further processing