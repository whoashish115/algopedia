---
title: Floyd-Warshall Algorithm
---

**Purpose:** Find the shortest path between every pair of vertices in a weighted graph (handles negative edges, not negative cycles), in O(V³) time.
### Algorithm
1. Build a `dist[][]` matrix where `dist[src][dst]` is the direct edge weight from `src` to `dst` (infinity if no edge, 0 if `src == dst`).
2. Pick each vertex `via` in turn as an "intermediate" vertex, one at a time.
3. For every pair `(src, dst)`, check if routing through `via` gives a shorter path: `dist[src][via] + dist[via][dst] < dist[src][dst]`.
4. If so, update `dist[src][dst]` to that shorter value.
5. After trying all vertices as intermediates, `dist[][]` holds the shortest path between every pair.
6. (Optional) If `dist[src][src] < 0` for any `src` after the algorithm runs, a negative-weight cycle exists.

### Code

```cpp
void floydWarshall(vector<vector<int>>& dist, int n) {
    for (int via = 0; via < n; via++) {
        for (int src = 0; src < n; src++) {
            for (int dst = 0; dst < n; dst++) {
                if (dist[src][via] != INT_MAX && dist[via][dst] != INT_MAX &&
                    dist[src][via] + dist[via][dst] < dist[src][dst]) {
                    dist[src][dst] = dist[src][via] + dist[via][dst];
                }
            }
        }
    }
}
```

### Paradigm

**Dynamic Programming.** `dist[src][dst]` after processing vertex `via` represents the optimal solution to the subproblem "shortest path from `src` to `dst` using only vertices seen so far as intermediates," built directly from the subproblem before `via` was added — a textbook 3D DP collapsed into a 2D in-place table.

### Complexity

- Time: O(V³)
- Space: O(V²)

### Proof of Correctness

**Claim:** After the outer loop finishes processing vertex `via`, `dist[src][dst]` equals the shortest path from `src` to `dst` using only the vertices processed so far (including `via`) as intermediate nodes.

**Proof by induction on the number of vertices processed:**

**Base case:** Before any iteration, `dist[src][dst]` is the direct edge weight (or infinity), which is exactly the shortest path using no intermediate vertices.

**Inductive step:** Assume `dist[src][dst]` is correct using only the intermediates processed before the current `via`. When processing vertex `via`, the true shortest path from `src` to `dst` allowed to use `via` as well either:

- doesn't use `via` at all, in which case its length is already captured by `dist[src][dst]` from before, or
- uses `via` exactly once (a shortest path never revisits a vertex), splitting into `src → via` and `via → dst`, both of which only use previously processed intermediates — and by the inductive hypothesis, both `dist[src][via]` and `dist[via][dst]` are already correct at this point.

The update rule `dist[src][dst] = min(dist[src][dst], dist[src][via] + dist[via][dst])` takes the better of these two cases, so `dist[src][dst]` becomes correct once `via` is included.

**Termination:** After every vertex has been used as `via` once, `dist[src][dst]` is correct using any vertex as an intermediate — i.e., the true shortest path for every pair. ∎

_(Note: correctness assumes no negative-weight cycles reachable between the pair; if one exists, `dist[src][src]` will go negative, which is used as the detection check.)_

### Variants / Use Cases

- **Transitive closure of a graph** → replace `min`/`+` with logical OR/AND to compute reachability between all pairs
- **Detecting negative cycles** → check diagonal `dist[src][src] < 0` after the algorithm runs
- **Path reconstruction** → maintain a `next[][]` matrix to reconstruct the actual shortest path between any pair
- **Minimax path / widest path problems** → replace `+` with `max` and `min` with `min` to find bottleneck shortest paths
- **Finding graph diameter** → take the max value over all finite entries in the final `dist[][]` matrix
- **All-pairs shortest paths on dense graphs** → preferred over running Dijkstra from every vertex when the graph is dense or has negative edges