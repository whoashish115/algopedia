---
title: Kirchhoff's Theorem
---

also known as Matrix Tree Theorem

**Purpose:** Count the number of distinct spanning trees of a graph via a single determinant computation, in O(V³) time.

### Algorithm

1. Build the Laplacian matrix `L = D - A` of the graph, where `D` is the diagonal degree matrix and `A` is the adjacency matrix.
2. Delete any one row and the corresponding column from `L` (which row/column doesn't matter), giving a reduced `(V-1)×(V-1)` matrix.
3. Compute the determinant of this reduced matrix using Gaussian elimination.
4. That determinant is exactly the number of spanning trees of the graph.
5. For a weighted count (sum of products of edge weights over all spanning trees), build `L` using edge weights instead of unit weights before deleting the row/column.

### Code

```cpp
long long spanningTreeCount(int n, vector<vector<long long>>& L) {
    vector<vector<double>> M(n - 1, vector<double>(n - 1));
    for (int i = 1; i < n; i++)
        for (int j = 1; j < n; j++)
            M[i - 1][j - 1] = L[i][j];

    double det = 1;
    for (int i = 0; i < n - 1; i++) {
        int pivot = i;
        for (int k = i + 1; k < n - 1; k++)
            if (fabs(M[k][i]) > fabs(M[pivot][i])) pivot = k;
        if (fabs(M[pivot][i]) < 1e-9) return 0;
        if (pivot != i) { swap(M[pivot], M[i]); det = -det; }

        det *= M[i][i];
        for (int k = i + 1; k < n - 1; k++) {
            double factor = M[k][i] / M[i][i];
            for (int j = i; j < n - 1; j++) M[k][j] -= factor * M[i][j];
        }
    }
    return llround(det);
}
```

### Paradigm

**Transform and Conquer.** A purely combinatorial counting problem (enumerating spanning trees) is transformed into an algebraic one (a matrix determinant), which is then solved efficiently with standard linear algebra instead of any combinatorial enumeration.

### Complexity

- Time: O(V³) (Gaussian elimination on the reduced Laplacian)
- Space: O(V²)

### Proof of Correctness

**Setup:** Let `B` be the oriented incidence matrix of the graph (`V × E`, arbitrary edge orientation). It's a standard fact that `L = B·Bᵀ`, and deleting one row of `B` (matching the deleted row/column of `L`) gives a reduced incidence matrix `B'` with `L_reduced = B'·B'ᵀ`.

**Cauchy-Binet expansion:** By the Cauchy-Binet formula, `det(L_reduced) = Σ_S det(B'_S)²`, summed over every `(V-1)`-sized subset `S` of edges, where `B'_S` is the square submatrix of `B'` formed by the columns in `S`.

**Key lemma:** For any `(V-1)`-edge subset `S`, `det(B'_S) = ±1` if `S` forms a spanning tree, and `0` otherwise.
_Proof:_ If `S` contains a cycle, the corresponding columns are linearly dependent (their signed sum around the cycle is the zero vector), so the determinant is 0. If `S` is a spanning tree, it has a leaf vertex whose row in `B'_S` has exactly one nonzero entry (±1); expanding the determinant along that row reduces the claim to the same statement for the tree with that leaf removed, which terminates at a single-edge base case with determinant ±1. By induction, `det(B'_S) = ±1`.

**Conclusion:** Every spanning tree contributes exactly `1` (its determinant squared) to the Cauchy-Binet sum, and every non-tree subset contributes `0`. Hence `det(L_reduced)` equals exactly the number of spanning trees. ∎

Built on: Gaussian Elimination.

### Variants / Use Cases

- **Weighted Matrix-Tree Theorem** → sum of products of edge weights over all spanning trees, used for weighted spanning tree counting
- **BEST theorem (directed graphs)** → counts Eulerian circuits in a directed graph using an arborescence-counting variant of the Matrix Tree Theorem
- **Electrical network theory** → Kirchhoff's original motivation, relating spanning tree counts to effective resistance between nodes
- **Phylogenetics / random spanning tree sampling** → generating uniformly random spanning trees via Laplacian-based techniques
- **Graph connectivity / reliability analysis** → spanning tree counts as a robustness measure of network topology
