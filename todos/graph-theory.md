---
title:  3. Graph Theory
---
A **graph** is a non-linear data structure consisting of a set of **vertices (nodes)** and a set of **edges** connecting pairs of vertices. It is used to model relationships between objects.

* **Vertices (V):** Objects or entities.
* **Edges (E):** Connections between vertices.
* **Order:** (n = |V|) (number of vertices)
* **Size:** (m = |E|) (number of edges)

## Types of Graphs

| Category                  | Type  | Definition  |
| :------------------------ | :--------------------------------------- | :------------------------------------------------------------ |
| **Based on Direction**    | **Undirected Graph**                     | A graph where edges have no direction. If vertex (u) is connected to (v), the connection is the same in both directions. |
|                           | **Directed Graph (Digraph)**             | A graph where every edge has a direction from one vertex to another, represented as ((u,v)).                             |
|                           | **Directed Acyclic Graph (DAG)**         | A directed graph with no directed cycles.                                                                                |
| **Based on Edge Weights** | **Unweighted Graph**                     | A graph where edges do not have weights; all edges are considered equal.                                                 |
|                           | **Weighted Graph**                       | A graph where each edge has an associated value such as cost, distance, or capacity.                                     |
| **Based on Edge Rules**   | **Simple Graph**                         | A graph with no self-loops and no multiple edges between the same pair of vertices.                                      |
|                           | **Multigraph**                           | A graph that allows multiple edges between the same pair of vertices.                                                    |
|                           | **Pseudograph**                          | A graph that allows both self-loops and multiple edges.                                                                  |
| **Based on Connectivity** | **Connected Graph**                      | A graph where every pair of vertices has at least one path between them.                                                 |
|                           | **Disconnected Graph**                   | A graph where some vertices cannot be reached from others.                                                               |
|                           | **Connected Component**                  | A maximal connected subgraph inside a disconnected graph.                                                                |
| **Based on Structure**    | **Complete Graph ((K_n))**               | A graph where every pair of distinct vertices is connected by exactly one edge.                                          |
|                           | **Regular Graph**                        | A graph where every vertex has the same degree.                                                                          |
|                           | **Bipartite Graph**                      | A graph whose vertices can be divided into two disjoint sets, with edges only between the two sets.                      |
|                           | **Complete Bipartite Graph ((K_{m,n}))** | A bipartite graph where every vertex of one set is connected to every vertex of the other set.                           |
| **Based on Cycles**       | **Cyclic Graph**                         | A graph that contains at least one cycle.                                                                                |
|                           | **Acyclic Graph**                        | A graph that contains no cycles.                                                                                         |
|                           | **Tree**                                 | A connected, undirected, acyclic graph with exactly (n-1) edges.                                                         |
|                           | **Forest**                               | An acyclic undirected graph consisting of multiple disconnected trees.                                                   |
---

# Graph Terminology

| Term                                | Definition                                                                              |
| :---------------------------------- | :-------------------------------------------------------------------------------------- |
| **Vertex (Node)**                   | A fundamental unit (object) in a graph.                                                 |
| **Edge**                            | A connection between two vertices.                                                      |
| **Adjacent Vertices**               | Two vertices connected by an edge.                                                      |
| **Incident Edge**                   | An edge connected to a particular vertex.                                               |
| **Path**                            | A sequence of vertices where consecutive vertices are connected by edges.               |
| **Path Length**                     | Number of edges in a path.                                                              |
| **Simple Path**                     | A path that does not repeat any vertex.                                                 |
| **Cycle (Circuit)**                 | A path that starts and ends at the same vertex without repeating intermediate vertices. |
| **Degree ((deg(v)))**               | Number of edges incident to a vertex in an undirected graph.                            |
| **Indegree ((deg^{-}(v)))**         | Number of incoming edges to a vertex in a directed graph.                               |
| **Outdegree ((deg^{+}(v)))**        | Number of outgoing edges from a vertex in a directed graph.                             |
| **Leaf (Pendant Vertex)**           | A vertex with degree 1.                                                                 |
| **Isolated Vertex**                 | A vertex with degree 0.                                                                 |
| **Bridge (Cut Edge)**               | An edge whose removal increases the number of connected components.                     |
| **Articulation Point (Cut Vertex)** | A vertex whose removal increases the number of connected components.                    |
| **Connected Component**             | A maximal connected subgraph of a graph.                                                |
| **Subgraph**                        | A graph formed using a subset of vertices and edges of another graph.                   |
| **Spanning Subgraph**               | A subgraph containing all vertices of the original graph.                               |
| **Spanning Tree**                   | A spanning subgraph that is a tree.                                                     |

---

# Important Theorems & Properties

| Theorem / Property                          | Statement                                                                                        |
| :------------------------------------------ | :----------------------------------------------------------------------------------------------- |
| **Handshake Lemma**                         | (\displaystyle \sum_{v\in V}deg(v)=2m). Therefore, the sum of all vertex degrees is always even. |
| **Bipartite Graph Theorem**                 | A graph is bipartite **iff** it contains **no odd cycle**.                                       |
| **Tree Property**                           | A graph is a tree **iff** it is connected and has exactly (n-1) edges.                           |
| **Forest Property**                         | A forest with (c) connected components has (n-c) edges.                                          |
| **Complete Graph Edges**                    | A complete graph with (n) vertices has (\displaystyle \frac{n(n-1)}{2}) edges.                   |
| **Maximum Edges (Simple Undirected Graph)** | (\displaystyle \frac{n(n-1)}{2}).                                                                |
| **Maximum Edges (Simple Directed Graph)**   | (n(n-1)).                                                                                        |

---

## 2 Graph Representation

| Representation   |   Space  | Edge lookup | Iterate neighbors | Best use                |
| :--------------- | :------: | :---------: | :---------------: | :---------------------- |
| Adjacency list   | $O(n+m)$ | $O(deg(v))$ |    $O(deg(v))$    | Sparse graphs           |
| Adjacency matrix | $O(n^2)$ |    $O(1)$   |       $O(n)$      | Dense graphs, small $n$ |
| Edge list        |  $O(m)$  |    $O(m)$   |       $O(m)$      | Kruskal, sorting edges  |

```cpp
int n, m;
vector<vector<int>> adj(n + 1);
vector<vector<pair<int,int>>> wadj(n + 1); // {to, weight}

// undirected edge
adj[u].push_back(v);
adj[v].push_back(u);

// directed edge
adj[u].push_back(v);

// weighted edge
wadj[u].push_back({v, w});
wadj[v].push_back({u, w}); // only for undirected weighted graph
```

---

## 3 Traversal

### DFS

Depth-first search explores as far as possible before backtracking.

**Time Complexity:** $O(n+m)$  
**Space Complexity:** $O(n)$

> **Idea:**  
> Mark a node visited, then recursively visit all unvisited neighbors.

```cpp
vector<int> visited;
vector<vector<int>> adj;

void dfs(int u) {
    visited[u] = 1;
    for (int v : adj[u]) {
        if (!visited[v]) dfs(v);
    }
}
```

### BFS

Breadth-first search explores level by level using a queue.

In an unweighted graph, BFS finds the shortest path in number of edges.

**Time Complexity:** $O(n+m)$  
**Space Complexity:** $O(n)$

> **Idea:**  
> Push the source into a queue, then expand nodes in increasing distance order.

```cpp
vector<int> dist, parent;
vector<vector<int>> adj;

void bfs(int s) {
    dist.assign(adj.size(), -1);
    parent.assign(adj.size(), -1);

    queue<int> q;
    dist[s] = 0;
    q.push(s);

    while (!q.empty()) {
        int u = q.front();
        q.pop();
        for (int v : adj[u]) {
            if (dist[v] == -1) {
                dist[v] = dist[u] + 1;
                parent[v] = u;
                q.push(v);
            }
        }
    }
}
```

### Connectivity check

A graph is connected iff DFS/BFS from one node visits all nodes.

```cpp
bool isConnected(int n, int start) {
    vector<int> vis(n + 1, 0);
    queue<int> q;
    vis[start] = 1;
    q.push(start);

    int cnt = 0;
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        cnt++;
        for (int v : adj[u]) {
            if (!vis[v]) {
                vis[v] = 1;
                q.push(v);
            }
        }
    }
    return cnt == n;
}
```

### Bipartite check (2-coloring)

Use 2-coloring. If adjacent nodes ever get the same color, the graph is not bipartite.

**Time Complexity:** $O(n+m)$

```cpp
bool isBipartite(int n) {
    vector<int> color(n + 1, -1);
    queue<int> q;

    for (int s = 1; s <= n; s++) {
        if (color[s] != -1) continue;

        color[s] = 0;
        q.push(s);

        while (!q.empty()) {
            int u = q.front();
            q.pop();

            for (int v : adj[u]) {
                if (color[v] == -1) {
                    color[v] = color[u] ^ 1;
                    q.push(v);
                } else if (color[v] == color[u]) {
                    return false;
                }
            }
        }
    }
    return true;
}
```

---

## 4 Topological Sort

Topological sorting is possible only in a **DAG**.

If there is an edge $u \to v$, then $u$ must appear before $v$.

### Kahn's algorithm (BFS)

**Time Complexity:** $O(n+m)$  
**Space Complexity:** $O(n)$

> **Idea:**  
> Repeatedly take nodes with indegree zero.

```cpp
vector<int> topoSortKahn(int n, vector<vector<int>>& adj) {
    vector<int> indeg(n + 1, 0), order;
    for (int u = 1; u <= n; u++) {
        for (int v : adj[u]) indeg[v]++;
    }

    queue<int> q;
    for (int i = 1; i <= n; i++) {
        if (indeg[i] == 0) q.push(i);
    }

    while (!q.empty()) {
        int u = q.front();
        q.pop();
        order.push_back(u);

        for (int v : adj[u]) {
            if (--indeg[v] == 0) q.push(v);
        }
    }

    if ((int)order.size() != n) return {}; // cycle exists
    return order;
}
```

### DFS-based topological sort

```cpp
vector<int> topo;
vector<int> vis, inStack;
bool hasCycle = false;

void dfsTopo(int u, vector<vector<int>>& adj) {
    vis[u] = 1;
    inStack[u] = 1;

    for (int v : adj[u]) {
        if (!vis[v]) dfsTopo(v, adj);
        else if (inStack[v]) hasCycle = true;
    }

    inStack[u] = 0;
    topo.push_back(u);
}

vector<int> topoSortDFS(int n, vector<vector<int>>& adj) {
    vis.assign(n + 1, 0);
    inStack.assign(n + 1, 0);
    topo.clear();
    hasCycle = false;

    for (int i = 1; i <= n; i++) {
        if (!vis[i]) dfsTopo(i, adj);
    }

    if (hasCycle) return {};
    reverse(topo.begin(), topo.end());
    return topo;
}
```

---

## 5 Shortest Paths

### BFS shortest path (unweighted)

For unweighted graphs, BFS gives shortest path distances.

**Time Complexity:** $O(n+m)$

```cpp
vector<int> shortestPathUnweighted(int n, int s, vector<vector<int>>& adj) {
    vector<int> dist(n + 1, -1);
    queue<int> q;
    dist[s] = 0;
    q.push(s);

    while (!q.empty()) {
        int u = q.front();
        q.pop();
        for (int v : adj[u]) {
            if (dist[v] == -1) {
                dist[v] = dist[u] + 1;
                q.push(v);
            }
        }
    }
    return dist;
}
```

### 0‑1 BFS

For graphs with edge weights 0 or 1, use a deque.

**Time Complexity:** $O(n+m)$

```cpp
vector<int> zeroOneBFS(int n, int s, vector<vector<pair<int,int>>>& adj) {
    const int INF = 1e9;
    vector<int> dist(n + 1, INF);
    deque<int> dq;
    dist[s] = 0;
    dq.push_front(s);

    while (!dq.empty()) {
        int u = dq.front();
        dq.pop_front();

        for (auto [v, w] : adj[u]) {
            if (dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                if (w == 0) dq.push_front(v);
                else dq.push_back(v);
            }
        }
    }
    return dist;
}
```

### Dijkstra

Works only when all edge weights are **non-negative**.

**Time Complexity:** $O((n+m)\log n)$  
**Space Complexity:** $O(n+m)$

> **Idea:**  
> Always expand the currently closest unprocessed node.

```cpp
vector<long long> dijkstra(int n, int s, vector<vector<pair<int,int>>>& adj) {
    const long long INF = (long long)4e18;
    vector<long long> dist(n + 1, INF);
    priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<pair<long long,int>>> pq;

    dist[s] = 0;
    pq.push({0, s});

    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();

        if (d != dist[u]) continue;

        for (auto [v, w] : adj[u]) {
            if (dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

### Bellman-Ford

Handles negative weights and detects negative cycles reachable from the source.

**Time Complexity:** $O(nm)$  
**Space Complexity:** $O(n)$

```cpp
struct Edge {
    int u, v;
    long long w;
};

pair<vector<long long>, bool> bellmanFord(int n, int s, vector<Edge>& edges) {
    const long long INF = (long long)4e18;
    vector<long long> dist(n + 1, INF);
    dist[s] = 0;

    for (int i = 1; i <= n - 1; i++) {
        bool changed = false;
        for (auto &e : edges) {
            if (dist[e.u] == INF) continue;
            if (dist[e.v] > dist[e.u] + e.w) {
                dist[e.v] = dist[e.u] + e.w;
                changed = true;
            }
        }
        if (!changed) break;
    }

    bool negCycle = false;
    for (auto &e : edges) {
        if (dist[e.u] != INF && dist[e.v] > dist[e.u] + e.w) {
            negCycle = true;
            break;
        }
    }

    return {dist, negCycle};
}
```

### Floyd-Warshall

All-pairs shortest paths for small $n$.

**Time Complexity:** $O(n^3)$  
**Space Complexity:** $O(n^2)$

```cpp
vector<vector<long long>> floydWarshall(int n, vector<vector<long long>> dist) {
    const long long INF = (long long)4e18;

    for (int k = 1; k <= n; k++) {
        for (int i = 1; i <= n; i++) {
            if (dist[i][k] == INF) continue;
            for (int j = 1; j <= n; j++) {
                if (dist[k][j] == INF) continue;
                dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
            }
        }
    }
    return dist;
}
```

---

## 6 Minimum Spanning Tree

A minimum spanning tree connects all vertices with minimum total edge weight.

### Kruskal's algorithm

Sort edges by weight and add them if they do not create a cycle.

**Time Complexity:** $O(m \log m)$  
**Space Complexity:** $O(n)$

> **Idea:**  
> Use DSU to check whether an edge connects two different components.

```cpp
struct DSU {
    vector<int> p, sz;

    DSU(int n) {
        p.resize(n + 1);
        sz.assign(n + 1, 1);
        iota(p.begin(), p.end(), 0);
    }

    int find(int x) {
        if (p[x] == x) return x;
        return p[x] = find(p[x]);
    }

    bool unite(int a, int b) {
        a = find(a);
        b = find(b);
        if (a == b) return false;
        if (sz[a] < sz[b]) swap(a, b);
        p[b] = a;
        sz[a] += sz[b];
        return true;
    }
};

struct Edge {
    int u, v;
    long long w;
};

long long kruskal(int n, vector<Edge>& edges) {
    sort(edges.begin(), edges.end(), [](const Edge& a, const Edge& b) {
        return a.w < b.w;
    });

    DSU dsu(n);
    long long cost = 0;

    for (auto &e : edges) {
        if (dsu.unite(e.u, e.v)) {
            cost += e.w;
        }
    }
    return cost;
}
```

### Prim's algorithm

Grow the MST from one start vertex.

**Time Complexity:** $O(m \log n)$  
**Space Complexity:** $O(n+m)$

```cpp
long long prim(int n, vector<vector<pair<int,int>>>& adj, int start = 1) {
    const long long INF = (long long)4e18;
    vector<int> vis(n + 1, 0);
    priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<pair<long long,int>>> pq;

    pq.push({0, start});
    long long cost = 0;

    while (!pq.empty()) {
        auto [w, u] = pq.top();
        pq.pop();

        if (vis[u]) continue;
        vis[u] = 1;
        cost += w;

        for (auto [v, wt] : adj[u]) {
            if (!vis[v]) pq.push({wt, v});
        }
    }
    return cost;
}
```

---

## 7 Strongly Connected Components

Strongly connected components are maximal sets in which every vertex can reach every other vertex.

### Kosaraju's algorithm

**Time Complexity:** $O(n+m)$  
**Space Complexity:** $O(n+m)$

> **Idea:**  
> 1. DFS on the original graph and store finish order.  
> 2. Reverse all edges.  
> 3. DFS in reverse finish order to get SCCs.

```cpp
vector<int> order, comp, vis;
vector<vector<int>> g, gr;

void dfs1(int u) {
    vis[u] = 1;
    for (int v : g[u]) {
        if (!vis[v]) dfs1(v);
    }
    order.push_back(u);
}

void dfs2(int u, int c) {
    comp[u] = c;
    for (int v : gr[u]) {
        if (comp[v] == -1) dfs2(v, c);
    }
}

int kosaraju(int n) {
    vis.assign(n + 1, 0);
    comp.assign(n + 1, -1);
    order.clear();

    for (int i = 1; i <= n; i++) {
        if (!vis[i]) dfs1(i);
    }

    int c = 0;
    reverse(order.begin(), order.end());
    for (int u : order) {
        if (comp[u] == -1) {
            dfs2(u, c++);
        }
    }
    return c;
}
```

### Tarjan's SCC (one-pass, optional)

**Time Complexity:** $O(n+m)$

> **Idea:**  
> Use DFS low-link values; pop SCC when low[u] == tin[u].

```cpp
vector<int> tin, low, stack, inStack;
vector<vector<int>> adj;
int timer, sccCount;

void tarjan(int u) {
    tin[u] = low[u] = ++timer;
    stack.push_back(u);
    inStack[u] = 1;

    for (int v : adj[u]) {
        if (!tin[v]) {
            tarjan(v);
            low[u] = min(low[u], low[v]);
        } else if (inStack[v]) {
            low[u] = min(low[u], tin[v]);
        }
    }

    if (low[u] == tin[u]) {
        sccCount++;
        while (true) {
            int v = stack.back(); stack.pop_back();
            inStack[v] = 0;
            comp[v] = sccCount; // assign component id
            if (v == u) break;
        }
    }
}
```

---

## 8 Bridges and Articulation Points

### Bridge

An edge whose removal increases the number of connected components.

### Articulation point

A vertex whose removal increases the number of connected components.

### Low-link DFS

**Time Complexity:** $O(n+m)$  
**Space Complexity:** $O(n+m)$

> **Idea:**  
> Compute discovery time `tin[u]` and low-link value `low[u]`.

```cpp
vector<vector<int>> adj;
vector<int> tin, low, vis;
vector<pair<int,int>> bridges;
vector<int> articulation;
int timer = 0;

void dfsBridge(int u, int p = -1) {
    vis[u] = 1;
    tin[u] = low[u] = ++timer;
    int children = 0;

    for (int v : adj[u]) {
        if (v == p) continue;

        if (vis[v]) {
            low[u] = min(low[u], tin[v]);
        } else {
            dfsBridge(v, u);
            low[u] = min(low[u], low[v]);

            if (low[v] > tin[u]) {
                bridges.push_back({u, v});
            }

            if (p != -1 && low[v] >= tin[u]) {
                articulation.push_back(u);
            }
            children++;
        }
    }

    if (p == -1 && children > 1) {
        articulation.push_back(u);
    }
}

void findBridgesAndArticulation(int n) {
    tin.assign(n + 1, 0);
    low.assign(n + 1, 0);
    vis.assign(n + 1, 0);
    bridges.clear();
    articulation.clear();
    timer = 0;

    for (int i = 1; i <= n; i++) {
        if (!vis[i]) dfsBridge(i);
    }
}
```

---

## 9 Disjoint Set Union (DSU)

DSU maintains a collection of disjoint sets.

It supports:

- `find(x)` = representative of the set  
- `unite(a, b)` = merge two sets  

**Amortized Complexity:** almost $O(1)$

```cpp
struct DSU {
    vector<int> p, sz;

    DSU(int n = 0) {
        init(n);
    }

    void init(int n) {
        p.resize(n + 1);
        sz.assign(n + 1, 1);
        iota(p.begin(), p.end(), 0);
    }

    int find(int x) {
        if (p[x] == x) return x;
        return p[x] = find(p[x]);
    }

    bool unite(int a, int b) {
        a = find(a);
        b = find(b);
        if (a == b) return false;
        if (sz[a] < sz[b]) swap(a, b);
        p[b] = a;
        sz[a] += sz[b];
        return true;
    }
};
```

### DSU with rollback (for offline dynamic connectivity)

> **Note:** Useful for problems where edges are added and removed over time (divide and conquer on time).

---

## 10 Trees

A tree is a connected graph with no cycles.

For a tree with $n$ nodes:

$$
m=n-1
$$

### Tree diameter

The diameter is the longest path in the tree.

> **Idea:**  
> Run BFS/DFS from any node, find the farthest node `A`.  
> Run BFS/DFS from `A`, and the farthest node from `A` is the other endpoint of the diameter.

```cpp
pair<int,int> farthestNode(int src, vector<vector<int>>& adj) {
    int n = (int)adj.size() - 1;
    vector<int> dist(n + 1, -1);
    queue<int> q;
    dist[src] = 0;
    q.push(src);

    while (!q.empty()) {
        int u = q.front();
        q.pop();
        for (int v : adj[u]) {
            if (dist[v] == -1) {
                dist[v] = dist[u] + 1;
                q.push(v);
            }
        }
    }

    int node = src, best = 0;
    for (int i = 1; i <= n; i++) {
        if (dist[i] > best) {
            best = dist[i];
            node = i;
        }
    }
    return {node, best};
}

int treeDiameter(vector<vector<int>>& adj) {
    auto [a, _1] = farthestNode(1, adj);
    auto [b, diameter] = farthestNode(a, adj);
    return diameter;
}
```

---

## 11 LCA by Binary Lifting

Lowest Common Ancestor is the deepest common ancestor of two nodes in a rooted tree.

**Preprocessing:** $O(n \log n)$  
**Query:** $O(\log n)$

```cpp
struct LCA {
    int n, LOG;
    vector<vector<int>> up;
    vector<int> depth;
    vector<vector<int>> adj;

    LCA(int n, vector<vector<int>> adj, int root = 1) {
        this->n = n;
        this->adj = adj;
        LOG = 1;
        while ((1 << LOG) <= n) LOG++;

        up.assign(LOG, vector<int>(n + 1, 0));
        depth.assign(n + 1, 0);
        dfs(root, root);
    }

    void dfs(int u, int p) {
        up[0][u] = p;
        for (int j = 1; j < LOG; j++) {
            up[j][u] = up[j - 1][up[j - 1][u]];
        }

        for (int v : adj[u]) {
            if (v == p) continue;
            depth[v] = depth[u] + 1;
            dfs(v, u);
        }
    }

    int lift(int u, int k) {
        for (int j = 0; j < LOG; j++) {
            if (k & (1 << j)) u = up[j][u];
        }
        return u;
    }

    int lca(int a, int b) {
        if (depth[a] < depth[b]) swap(a, b);
        a = lift(a, depth[a] - depth[b]);

        if (a == b) return a;

        for (int j = LOG - 1; j >= 0; j--) {
            if (up[j][a] != up[j][b]) {
                a = up[j][a];
                b = up[j][b];
            }
        }
        return up[0][a];
    }
};
```

---

## 12 Eulerian Path and Circuit

- **Eulerian circuit:** A cycle that uses every edge exactly once (returns to start).  
  Exists in an undirected graph iff every vertex has even degree and the graph is connected (ignoring isolated vertices).  
  In directed graph, every vertex has indegree = outdegree and all non‑zero degree vertices belong to a single SCC.

- **Eulerian path:** Uses every edge exactly once (does not need to return).  
  In undirected graph, exactly 0 or 2 vertices have odd degree.  
  In directed graph, at most one vertex has outdegree = indegree+1, at most one has indegree = outdegree+1, all others equal.

**Algorithm:** Hierholzer's algorithm (DFS with edge deletion) – $O(m)$.

---

## 13 Bipartite Matching (Kuhn's algorithm)

Maximum bipartite matching in an unweighted bipartite graph.

**Time Complexity:** $O(n \cdot m)$ (but often faster)

> **Idea:**  
> Try to find augmenting paths for each left vertex.

```cpp
vector<vector<int>> adj; // left -> right
vector<int> matchR, seen;

bool tryKuhn(int v) {
    if (seen[v]) return false;
    seen[v] = 1;
    for (int to : adj[v]) {
        if (matchR[to] == -1 || tryKuhn(matchR[to])) {
            matchR[to] = v;
            return true;
        }
    }
    return false;
}

int maxMatching(int nL, int nR) {
    matchR.assign(nR + 1, -1);
    int matching = 0;
    for (int v = 1; v <= nL; v++) {
        seen.assign(nL + 1, 0);
        if (tryKuhn(v)) matching++;
    }
    return matching;
}
```

---

## 14 Maximum Flow (Dinic)

**Time Complexity:** $O(E \cdot V^2)$ in general, but $O(E \sqrt{V})$ for unit capacities.

```cpp
struct Dinic {
    struct Edge {
        int to, rev;
        long long cap;
    };

    int n;
    vector<vector<Edge>> g;
    vector<int> level, it;

    Dinic(int n) : n(n), g(n), level(n), it(n) {}

    void addEdge(int v, int to, long long cap) {
        Edge a{to, (int)g[to].size(), cap};
        Edge b{v, (int)g[v].size(), 0};
        g[v].push_back(a);
        g[to].push_back(b);
    }

    bool bfs(int s, int t) {
        fill(level.begin(), level.end(), -1);
        queue<int> q;
        level[s] = 0;
        q.push(s);
        while (!q.empty()) {
            int v = q.front(); q.pop();
            for (auto &e : g[v]) {
                if (e.cap > 0 && level[e.to] == -1) {
                    level[e.to] = level[v] + 1;
                    q.push(e.to);
                }
            }
        }
        return level[t] != -1;
    }

    long long dfs(int v, int t, long long f) {
        if (v == t) return f;
        for (int &i = it[v]; i < (int)g[v].size(); i++) {
            Edge &e = g[v][i];
            if (e.cap > 0 && level[v] < level[e.to]) {
                long long pushed = dfs(e.to, t, min(f, e.cap));
                if (pushed > 0) {
                    e.cap -= pushed;
                    g[e.to][e.rev].cap += pushed;
                    return pushed;
                }
            }
        }
        return 0;
    }

    long long maxFlow(int s, int t) {
        long long flow = 0;
        while (bfs(s, t)) {
            fill(it.begin(), it.end(), 0);
            while (true) {
                long long pushed = dfs(s, t, (long long)4e18);
                if (!pushed) break;
                flow += pushed;
            }
        }
        return flow;
    }
};
```

---

## 15 Common Graph Facts

| Property                            | Fact                                   |
| :---------------------------------- | :------------------------------------- |
| Tree                                | Connected and acyclic                  |
| Forest                              | Acyclic graph                          |
| DAG                                 | Directed graph with no directed cycle  |
| Bipartite graph                     | No odd cycle                           |
| Complete graph $K_n$                | Number of edges is $\dfrac{n(n-1)}{2}$ |
| Tree with $n$ nodes                 | Exactly $n-1$ edges                    |
| Connected graph                     | Has at least $n-1$ edges               |
| Sum of indegrees in directed graph  | $m$                                    |
| Sum of outdegrees in directed graph | $m$                                    |

---

## 16 When to use what

| Problem type                                | Typical algorithm        |
| :------------------------------------------ | :----------------------- |
| Reachability / component count              | DFS / BFS                |
| Shortest path in unweighted graph           | BFS                      |
| Shortest path with 0/1 weights              | 0‑1 BFS                  |
| Shortest path with non‑negative weights     | Dijkstra                 |
| Negative weights (no neg. cycles)           | Bellman‑Ford             |
| All‑pairs shortest path, small $n$          | Floyd‑Warshall           |
| Minimum spanning tree                       | Kruskal / Prim           |
| SCC in directed graph                       | Kosaraju / Tarjan        |
| Bridges / articulation points               | Low‑link DFS             |
| Tree ancestor queries                       | LCA / binary lifting     |
| Dynamic connectivity (union only)           | DSU                      |
| Eulerian path / circuit                     | Hierholzer               |
| Bipartite maximum matching                  | Kuhn (DFS augmenting)    |
| Maximum flow / min‑cut                      | Dinic                    |
```
