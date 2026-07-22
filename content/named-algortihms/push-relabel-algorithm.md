---
title: Push-Relabel Algorithm
---

**Purpose:** Compute the maximum flow from source to sink in a flow network using local push and relabel operations instead of global augmenting paths, in O(V²E) time (O(V³) with FIFO/highest-label selection).
### Algorithm
1. Assign a height label to every vertex: the source gets height `V`, all others start at 0.
2. Saturate every edge leaving the source, creating "excess" flow at the neighboring vertices (this is a "preflow," not a valid flow yet).
3. While some vertex (other than source/sink) has positive excess:
   - If it has a residual neighbor at a strictly lower height, **push** as much excess as possible along that edge.
   - If no such neighbor exists, **relabel** the vertex: raise its height to `1 + minimum height among residual neighbors`.
4. Once no vertex has excess left, the preflow satisfies flow conservation everywhere except source/sink — it is now a valid maximum flow.

### Code
```cpp
struct PushRelabel {
    struct Edge { int to, cap, flow; };
    vector<Edge> edges;
    vector<vector<int>> adj;
    vector<int> height, excess;
    int n;

    PushRelabel(int n) : n(n), adj(n), height(n), excess(n, 0) {}

    void addEdge(int u, int v, int cap) {
        adj[u].push_back(edges.size()); edges.push_back({v, cap, 0});
        adj[v].push_back(edges.size()); edges.push_back({u, 0, 0});
    }

    void push(int u, int id) {
        int v = edges[id].to;
        int d = min(excess[u], edges[id].cap - edges[id].flow);
        edges[id].flow += d;
        edges[id ^ 1].flow -= d;
        excess[u] -= d;
        excess[v] += d;
    }

    void relabel(int u) {
        int minH = INT_MAX;
        for (int id : adj[u])
            if (edges[id].cap > edges[id].flow) minH = min(minH, height[edges[id].to]);
        if (minH < INT_MAX) height[u] = minH + 1;
    }

    int maxflow(int s, int t) {
        height[s] = n;
        for (int id : adj[s]) {
            excess[s] += edges[id].cap;
            push(s, id);
        }
        vector<int> order;
        for (int i = 0; i < n; i++) if (i != s && i != t) order.push_back(i);

        bool moved = true;
        while (moved) {
            moved = false;
            for (int u : order) {
                while (excess[u] > 0) {
                    bool pushed = false;
                    for (int id : adj[u]) {
                        if (edges[id].cap > edges[id].flow && height[u] == height[edges[id].to] + 1) {
                            push(u, id);
                            pushed = true;
                            moved = true;
                            if (excess[u] == 0) break;
                        }
                    }
                    if (!pushed) { relabel(u); moved = true; }
                    else if (excess[u] == 0) break;
                }
            }
        }
        return excess[t];
    }
};
```

### Paradigm
**Greedy.** Every operation (push or relabel) is a locally optimal move made without global path search, relying on the height-function invariant to guarantee these local moves collectively converge to a global maximum flow.

### Complexity
- Time: O(V²E) generic; O(V³) with FIFO or highest-label vertex selection
- Space: O(V + E)

### Proof of Correctness
**Height invariant:** Throughout the algorithm, `height[u] ≤ height[v] + 1` for every residual edge `(u, v)`. Pushes only occur when `height[u] = height[v] + 1` exactly, so a push never violates this. Relabeling raises `height[u]` to exactly `1 + min` over residual neighbors, restoring the invariant for `u`.

**No infinite loop:** Each relabel strictly increases a vertex's height, and height is bounded above by `2V - 1` (a standard result from the invariant plus the fact that excess can only reach the sink or flow back to the source), so the total number of relabels is O(V²). Between relabels, only a bounded number of saturating pushes can occur before a relabel becomes necessary, bounding total work.

**Why zero excess everywhere (except source/sink) implies max flow:** By the height invariant, `height[source] = V` is fixed and higher than any other vertex could reach if a path to the sink still existed in the residual graph with all intermediate heights bounded by `2V`. When no vertex has excess, flow conservation holds at every internal vertex, making the preflow a genuine flow. If a residual augmenting path from source to sink still existed, the height-invariant would force a contradiction (heights would need to strictly decrease by more than 1 per edge along any residual path from a height-V vertex to a height-0 vertex over a path shorter than V, which the invariant forbids). Hence no augmenting path remains, and by the max-flow min-cut theorem, the flow is maximum. ∎

### Variants / Use Cases
- **FIFO push-relabel** → process vertices with excess in a queue, achieving O(V³)
- **Highest-label push-relabel** → always process the vertex with the largest height first, fast in practice
- **Gap heuristic / global relabeling** → practical speedups that skip unreachable height levels
- **Min-cut computation** → vertices reachable from source in the final residual graph give a minimum cut
- **Image segmentation, bipartite matching at scale** → push-relabel variants often outperform augmenting-path methods on dense graphs
