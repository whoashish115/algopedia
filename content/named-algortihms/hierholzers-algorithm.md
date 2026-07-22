---
title: Hierholzer's Algorithm
---

**Purpose:** Find an Eulerian circuit (or path) in a graph that uses every edge exactly once, in O(E) time.
### Algorithm
1. Check the Eulerian condition first: for a circuit, every vertex must have even degree (or in-degree = out-degree for directed graphs) and the graph must be connected on its edges; for a path, exactly two vertices may have odd degree.
2. Start at any vertex (or one of the odd-degree vertices, if finding a path).
3. Walk along unused edges arbitrarily, marking each as used, until stuck — this only happens back at the starting vertex, since every vertex has even degree so you always have an unused edge to leave by until you return to start.
4. This walk is a closed sub-circuit. Push its vertices onto a stack as you go.
5. While popping vertices off the stack to build the final answer, whenever a popped vertex still has unused edges, immediately walk a new sub-circuit from it and splice those vertices in before continuing to pop.
6. Repeat until every edge has been used. The final popped order is the Eulerian circuit.

### Code
```cpp
vector<int> hierholzer(int n, int totalEdges, vector<vector<pair<int,int>>>& adj, int start) {
    vector<bool> usedEdge(totalEdges, false);
    vector<int> ptr(n, 0);
    stack<int> st;
    vector<int> circuit;

    st.push(start);
    while (!st.empty()) {
        int u = st.top();
        while (ptr[u] < (int)adj[u].size() && usedEdge[adj[u][ptr[u]].second]) ptr[u]++;

        if (ptr[u] == (int)adj[u].size()) {
            circuit.push_back(u);
            st.pop();
        } else {
            auto [v, eid] = adj[u][ptr[u]];
            usedEdge[eid] = true;
            ptr[u]++;
            st.push(v);
        }
    }
    reverse(circuit.begin(), circuit.end());
    return circuit;
}
```

### Paradigm
**Decrease and Conquer.** Each detected sub-circuit is spliced into the growing answer, reducing the unsolved portion of the graph (remaining unused edges) at every step until nothing is left.

### Complexity
- Time: O(E)
- Space: O(V + E)

### Proof of Correctness
**Why you always return to the start:** Since every vertex has even degree, every time the walk enters a vertex other than the start via one edge, an unused edge to leave by is always still available (edges are used in pairs at intermediate vertices). So the walk can only get stuck at the start, producing a valid closed sub-circuit.

**Why splicing produces a full Eulerian circuit:** Suppose a vertex `v` on the current circuit still has unused edges. Those unused edges, by the same even-degree argument applied to the remaining unused subgraph, form another closed sub-circuit starting and ending at `v`. Splicing this sub-circuit in at `v`'s position produces a longer closed walk that still uses every edge exactly once so far, and still starts and ends at the same vertex. Since the graph is connected on its edges, repeating this process must eventually incorporate every edge, since any leftover unused edge is reachable from some vertex already in the circuit. ∎

### Variants / Use Cases
- **Eulerian path (not circuit)** → start the algorithm from one of the two odd-degree vertices
- **De Bruijn sequence construction** → build a De Bruijn graph and find its Eulerian circuit
- **Genome assembly (k-mer reconstruction)** → sequencing reads modeled as edges in a De Bruijn graph, reconstructed via Eulerian path
- **Chinese Postman Problem** → uses Eulerian circuits after adding duplicate edges to make all degrees even
- **Reconstructing itineraries from tickets (LeetCode "Reconstruct Itinerary")** → direct application
