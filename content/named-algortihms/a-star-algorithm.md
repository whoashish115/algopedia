---
title: A-star Algorithm
---

**Purpose:** Find the shortest path from a start node to a goal node in a weighted graph, using a heuristic estimate of remaining distance to guide the search toward the goal, in O(E log V) time worst case (typically far less in practice with a good heuristic).

### Algorithm
1. Maintain a priority queue of nodes ordered by `f(n) = g(n) + h(n)`, where `g(n)` is the known cheapest cost from start to `n`, and `h(n)` is a heuristic estimate of the cost from `n` to the goal.
2. Start by placing the start node in the queue with `g = 0`.
3. Repeatedly pop the node with the smallest `f` value.
4. If it's the goal, stop — the path has been found.
5. Otherwise, for each neighbor, compute the tentative cost of reaching it through the current node. If this is better than any previously known cost, update it, record the current node as its parent, and push it into the queue with its new `f` value.
6. Repeat until the goal is popped (success) or the queue empties (no path exists). Reconstruct the path by following parent pointers backward from the goal.

### Code
```cpp
struct AStar {
    struct Edge { int to, weight; };
    vector<vector<Edge>> adj;
    int n;

    AStar(int n) : n(n), adj(n) {}

    void addEdge(int u, int v, int w) {
        adj[u].push_back({v, w});
    }

    vector<int> findPath(int start, int goal, function<int(int)> heuristic) {
        vector<int> g(n, INT_MAX), parent(n, -1);
        vector<bool> closed(n, false);
        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> open;

        g[start] = 0;
        open.push({heuristic(start), start});

        while (!open.empty()) {
            auto [f, u] = open.top(); open.pop();
            if (closed[u]) continue;
            closed[u] = true;
            if (u == goal) break;

            for (auto& e : adj[u]) {
                int tentative = g[u] + e.weight;
                if (tentative < g[e.to]) {
                    g[e.to] = tentative;
                    parent[e.to] = u;
                    open.push({tentative + heuristic(e.to), e.to});
                }
            }
        }

        vector<int> path;
        if (g[goal] == INT_MAX) return path;
        for (int v = goal; v != -1; v = parent[v]) path.push_back(v);
        reverse(path.begin(), path.end());
        return path;
    }
};
```

### Paradigm
**Branch and Bound.** A* explores a search tree of partial paths, using `f(n) = g(n) + h(n)` as a lower-bound estimate on total path cost to prune (deprioritize) branches that cannot possibly beat the best solution found so far — the same principle as classical branch and bound, specialized with a problem-specific heuristic bound.

### Complexity
- Time: O(E log V) worst case (degenerates to Dijkstra when `h = 0`); typically far fewer nodes expanded with an informative heuristic
- Space: O(V)

### Proof of Correctness
**Claim:** If `h` is admissible (never overestimates the true remaining cost) and consistent (`h(u) ≤ weight(u, v) + h(v)` for every edge), A* returns the true shortest path.

**Invariant:** Whenever a node `u` is popped from the open queue (closed), `g[u]` equals the true shortest distance from start to `u`.

**Proof by induction on pop order:** The start node trivially has `g = 0`, correct. Assume the invariant holds for all nodes popped before `u`. Since `h` is consistent, `f` values are non-decreasing along any path, so any path to `u` through an unclosed node would have `f ≥ f(u)` at the time `u` is popped — meaning no cheaper route to `u` remains undiscovered. Hence `g[u]` at pop time is already the minimum possible, proving the invariant.

**Termination and optimality:** By the invariant, when the goal is popped, `g[goal]` is the true shortest distance, and the parent pointers trace out an actual shortest path. ∎

### Variants / Use Cases
- **Weighted A*** → inflates `h` by a factor `> 1` to trade optimality for speed
- **IDA* (Iterative Deepening A*)** → depth-first variant with O(V) space, repeatedly deepening an `f`-value threshold
- **Bidirectional A*** → searches simultaneously from start and goal to meet in the middle
- **Jump Point Search** → grid-specific pruning that skips symmetric paths for large speedups
- **D* / D* Lite** → dynamic replanning variants for changing graphs, used in robotics path planning
- **Pathfinding in games, GPS navigation, robotics motion planning** → primary real-world applications