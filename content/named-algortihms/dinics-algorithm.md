---
title: Dinic's Algorithm
---

**Purpose:** Compute the maximum flow from source to sink in a flow network, in O(V²E) time (O(E√V) on unit-capacity graphs), using phases of level graphs and blocking flows.

### Algorithm
1. Build a level graph via BFS from the source: assign each vertex a level (its shortest distance from source using only residual edges), keeping only edges that go from level `i` to level `i+1`.
2. If the sink has no level (unreachable), stop — the current flow is maximum.
3. Find a blocking flow within this level graph using DFS: repeatedly push flow along level-respecting paths from source to sink until no more augmenting paths exist in this level graph, using pointers per vertex to skip already-exhausted edges (current-arc optimization).
4. Add the blocking flow found to the total flow, and update the residual graph.
5. Rebuild the level graph from scratch and repeat from step 1.

### Code
```cpp
struct Dinic {
    struct Edge { int to, cap, flow; };
    vector<Edge> edges;
    vector<vector<int>> adj;
    vector<int> level, ptr;
    int n, src, sink;

    Dinic(int n) : n(n), adj(n), level(n), ptr(n) {}

    void addEdge(int u, int v, int cap) {
        adj[u].push_back(edges.size()); edges.push_back({v, cap, 0});
        adj[v].push_back(edges.size()); edges.push_back({u, 0, 0});
    }

    bool bfs() {
        fill(level.begin(), level.end(), -1);
        queue<int> q; q.push(src); level[src] = 0;
        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int id : adj[u]) {
                if (level[edges[id].to] == -1 && edges[id].cap > edges[id].flow) {
                    level[edges[id].to] = level[u] + 1;
                    q.push(edges[id].to);
                }
            }
        }
        return level[sink] != -1;
    }

    int dfs(int u, int pushed) {
        if (u == sink || pushed == 0) return pushed;
        for (int& i = ptr[u]; i < (int)adj[u].size(); i++) {
            int id = adj[u][i], v = edges[id].to;
            if (level[v] != level[u] + 1 || edges[id].cap <= edges[id].flow) continue;
            int d = dfs(v, min(pushed, edges[id].cap - edges[id].flow));
            if (d > 0) {
                edges[id].flow += d;
                edges[id ^ 1].flow -= d;
                return d;
            }
        }
        return 0;
    }

    int maxflow(int s, int t) {
        src = s; sink = t;
        int flow = 0;
        while (bfs()) {
            fill(ptr.begin(), ptr.end(), 0);
            while (int pushed = dfs(src, INT_MAX)) flow += pushed;
        }
        return flow;
    }
};
```

### Paradigm
**Greedy (phase-based).** Each phase greedily saturates a blocking flow in the current level graph and never revisits that phase's decisions, relying on the level graph strictly deepening each phase to bound total work.

### Complexity
- Time: O(V²E) generally; O(E√V) on unit-capacity graphs (e.g. bipartite matching)
- Space: O(V + E)

### Proof of Correctness
**Why blocking flow + rebuilding gives max flow:** A blocking flow saturates at least one edge on every source-to-sink path within the current level graph, so after adding it, no augmenting path of the current shortest length remains. The algorithm terminates only when the sink becomes completely unreachable in the residual graph, at which point — exactly as in the general Ford-Fulkerson argument — the reachable set from the source forms one side of a saturated minimum cut, proving the flow is maximum.

**Why the number of phases is bounded by O(V):** After each phase, the shortest augmenting-path distance from source to sink in the residual graph strictly increases. This is because the blocking flow eliminates every path of the current shortest length, and any new augmenting path must use at least one back-edge introduced by this phase's flow, which provably cannot shorten the path (back-edges only increase apparent path length in this construction). Since the shortest path length is bounded by `V - 1`, there are at most O(V) phases.

**Per-phase cost:** Each phase's BFS costs O(E), and the DFS blocking-flow computation costs O(VE) due to the current-arc optimization (each edge is "given up on" at most once per phase). This gives O(V²E) total. ∎

### Variants / Use Cases
- **Unit-capacity graphs (bipartite matching)** → runs in O(E√V), matching Hopcroft-Karp's bound
- **Link-cut tree optimization** → improves blocking flow computation to O(VE log V) using dynamic trees
- **Min-cost flow extensions** → level-graph ideas combined with shortest-path-based cost minimization
- **Image segmentation, project selection** → practical max-flow applications where Dinic's speed matters
- **ISAP (Improved Shortest Augmenting Path)** → related algorithm avoiding full level graph rebuilds each phase