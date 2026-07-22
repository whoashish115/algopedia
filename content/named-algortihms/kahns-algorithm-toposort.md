---
title: Kahn's Algorithm & TopoSort
---

## Kahn's Algorithm (BFS-based Topological Sort)
**Purpose:** Produce a linear ordering of vertices in a Directed Acyclic Graph (DAG) such that every edge goes from an earlier vertex to a later one, in O(V + E) time.

### Algorithm
1. Compute the in-degree (number of incoming edges) of every vertex.
2. Push all vertices with in-degree 0 into a queue — these have no dependencies and can safely go first.
3. Repeatedly pop a vertex from the queue and add it to the topological order.
4. For each neighbor of the popped vertex, decrement its in-degree by 1 (since one of its dependencies is now resolved).
5. If a neighbor's in-degree drops to 0, push it into the queue.
6. Repeat until the queue is empty. If the resulting order contains all `V` vertices, it's a valid topological sort; if fewer, the graph has a cycle.

### Code
```cpp
vector<int> kahnTopoSort(int n, vector<vector<int>>& adj) {
    vector<int> indegree(n, 0);
    for (int u = 0; u < n; u++)
        for (int v : adj[u]) indegree[v]++;

    queue<int> q;
    for (int i = 0; i < n; i++) if (indegree[i] == 0) q.push(i);

    vector<int> order;
    while (!q.empty()) {
        int u = q.front(); q.pop();
        order.push_back(u);

        for (int v : adj[u]) {
            if (--indegree[v] == 0) q.push(v);
        }
    }

    if ((int)order.size() != n) return {}; // cycle detected
    return order;
}
```

### Paradigm
**Greedy.** At every step it picks any vertex with zero remaining dependencies and commits to placing it next, relying on the fact that a vertex with in-degree 0 can always safely go before all its remaining neighbors without violating any edge constraint.

### Complexity
- Time: O(V + E)
- Space: O(V)

### Proof of Correctness
**Claim:** The order produced is a valid topological order, and the algorithm processes all `V` vertices if and only if the graph is acyclic.

**Why the order respects all edges:** A vertex `v` is only pushed into the queue after every one of its in-edges has been "resolved" (its predecessor already placed in `order`, causing `v`'s in-degree to drop to 0). So by construction, every predecessor of `v` appears in `order` strictly before `v`, meaning every edge `u → v` has `u` appearing before `v`. This holds for every vertex processed, so the final order respects all edges.

**Why every vertex is eventually processed (if the graph is acyclic):** Suppose the graph is acyclic but some vertex `x` is never added to the queue. Since the graph is a DAG, `x` has some finite set of ancestors, tracing back must terminate at a vertex with in-degree 0 (a DAG always has at least one source). That source gets processed first, decrementing its neighbors' in-degrees, eventually propagating down every dependency chain leading to `x`. Since there's no cycle to create a circular wait, every ancestor of `x` is eventually processed, so `x`'s in-degree eventually reaches 0 and it gets pushed — contradiction. So all vertices are processed.

**Why a cycle causes fewer than `V` vertices to be output:** Every vertex in a cycle depends on another vertex in the same cycle, so none of them can ever reach in-degree 0 (each one's last required predecessor is inside the cycle itself, which never gets processed first). So cycle vertices are never pushed into the queue, and `order.size() < n` correctly flags this. ∎

### Variants / Use Cases
- **DFS-based topological sort** → alternative approach using post-order DFS and reversing the finish order, doesn't need explicit in-degree tracking
- **Cycle detection in directed graphs** → a direct byproduct: if Kahn's can't process all vertices, the graph has a cycle
- **Course scheduling / build systems** → classic real-world application where tasks have prerequisite dependencies
- **Parallel task scheduling** → vertices with in-degree 0 at any point can be processed simultaneously, useful for parallelizing independent tasks
- **Lexicographically smallest topological order** → replace the queue with a min-heap to always pick the smallest available vertex next

---
## Topological Sort (DFS-based)
**Purpose:** Produce a linear ordering of vertices in a Directed Acyclic Graph (DAG) such that every edge goes from an earlier vertex to a later one, in O(V + E) time using DFS finish order.

### Algorithm
1. Maintain a `visited` array and an empty stack (or list) to record finish order.
2. Run DFS from every unvisited vertex.
3. In the DFS, recurse into all unvisited neighbors first before doing anything else.
4. Once all neighbors of a vertex are fully explored, push that vertex onto the stack (it "finishes").
5. After DFS completes from all starting points, pop everything off the stack — this gives the topological order.
6. (Cycle check, if needed) If a DFS ever revisits a vertex that's still "in progress" (on the current recursion path, not yet finished), the graph has a cycle and no valid topological order exists.

### Code
```cpp
void dfsTopo(int u, vector<vector<int>>& adj, vector<bool>& visited, stack<int>& finishOrder) {
    visited[u] = true;
    for (int v : adj[u]) {
        if (!visited[v]) dfsTopo(v, adj, visited, finishOrder);
    }
    finishOrder.push(u);
}

vector<int> dfsTopoSort(int n, vector<vector<int>>& adj) {
    vector<bool> visited(n, false);
    stack<int> finishOrder;

    for (int i = 0; i < n; i++) {
        if (!visited[i]) dfsTopo(i, adj, visited, finishOrder);
    }

    vector<int> order;
    while (!finishOrder.empty()) {
        order.push_back(finishOrder.top());
        finishOrder.pop();
    }
    return order;
}
```

### Paradigm
**Decrease and Conquer.** Each DFS call fully resolves a vertex's entire dependent subtree before finishing that vertex itself, recursively reducing the ordering problem to smaller unresolved subgraphs until the base case (a vertex with no unvisited neighbors) is hit.

### Complexity
- Time: O(V + E)
- Space: O(V)

### Proof of Correctness
**Claim:** For every directed edge `u → v`, `u` finishes after `v` in the DFS — so reversing finish order places `u` before `v`, giving a valid topological order.

**Proof:** Consider any edge `u → v` in a DAG. When DFS visits `u` and looks at this edge, one of two cases holds:
- `v` is unvisited: DFS recurses into `v` immediately, and since DFS fully completes a recursive call before returning, `v` (and everything reachable from it) finishes before control returns to `u`. So `v` finishes before `u`.
- `v` is already visited: since the graph has no cycles, `v` cannot be an ancestor of `u` in the current DFS path (that would create a cycle via the edge `u → v`). So `v` must have already finished in a previous, unrelated DFS branch — meaning `v` finished before `u` even started this edge check.

In both cases, `v` finishes before `u`. Since finish order is pushed onto a stack, popping the stack (reverse finish order) places `u` before `v` — exactly satisfying the edge direction requirement. This holds for every edge, so the final order is a valid topological sort.

**Why this only works on a DAG:** If a cycle exists, some vertex `v` would need to finish before an ancestor `u` on its own cycle, while also requiring `u` to finish before `v` (going the other way around the cycle) — a contradiction, which is exactly why cycles break topological ordering. ∎

### Variants / Use Cases
- **Kahn's Algorithm (BFS-based)** → alternative approach using in-degree tracking and a queue, doesn't require recursion or a stack
- **Cycle detection in directed graphs** → track a "currently in recursion stack" state; revisiting such a vertex confirms a cycle
- **Course scheduling / build systems** → classic real-world application where tasks have prerequisite dependencies
- **Dependency resolution (package managers, compilers)** → DFS-based order is common when dependencies are traversed recursively
- **Strongly Connected Components (Kosaraju's)** → reuses this exact DFS-finish-order idea as its first pass