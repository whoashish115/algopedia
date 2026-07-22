---
title: Gaussian elimination
---

**Purpose:** Solve a system of linear equations `Ax = b` (or compute rank/determinant) by reducing the matrix to upper triangular form, in O(n^3) time for an n×n system.

### Algorithm

1. For each column (acting as the current pivot), pick the row below (or at) it with the largest-magnitude entry in that column, and swap it into the current row (partial pivoting, for numerical stability).
2. If every entry in that column is (numerically) zero, skip it — the system is singular or has dependent variables.
3. Otherwise, eliminate that variable from every row below by subtracting the right multiple of the pivot row.
4. Move to the next column and repeat, until the matrix is in upper triangular (row echelon) form.
5. Starting from the last row, solve for each variable by substituting in the already-known values of later variables (back-substitution), moving upward row by row.

### Code

```cpp
vector<double> gaussianElimination(vector<vector<double>> a, vector<double> b) {
    int n = a.size();
    for (int i = 0; i < n; i++) a[i].push_back(b[i]);

    for (int col = 0; col < n; col++) {
        int piv = col;
        for (int row = col + 1; row < n; row++)
            if (fabs(a[row][col]) > fabs(a[piv][col])) piv = row;
        swap(a[col], a[piv]);
        if (fabs(a[col][col]) < 1e-9) continue;

        for (int row = col + 1; row < n; row++) {
            double factor = a[row][col] / a[col][col];
            for (int c = col; c <= n; c++) a[row][c] -= factor * a[col][c];
        }
    }

    vector<double> x(n, 0);
    for (int row = n - 1; row >= 0; row--) {
        double sum = a[row][n];
        for (int c = row + 1; c < n; c++) sum -= a[row][c] * x[c];
        if (fabs(a[row][row]) < 1e-9) continue;
        x[row] = sum / a[row][row];
    }
    return x;
}
```

### Paradigm

**Decrease and Conquer.** Each pivot step eliminates one variable, reducing the problem to a strictly smaller (n-1)-sized triangular subsystem, which is then solved by successively substituting back up through the reduced system.

### Complexity

- Time: O(n^3)
- Space: O(n^2)

### Proof of Correctness

**Why elimination preserves the solution set:** row swaps, row scaling, and adding a multiple of one row to another are all elementary row operations, which are well-known to preserve the solution set of a linear system exactly (each operation is invertible and only recombines existing equations).

**Why triangular form is reached correctly:** by induction on the column index `k` — after processing column `k`, every row below the `k`-th pivot has a zero in columns `0..k`, and the augmented system remains equivalent to the original (by the elementary-operation argument above). After all `n` columns are processed, the system is in triangular form and still equivalent to the original.

**Why back-substitution is correct:** by induction from the last row upward — the last equation has only one unknown and is solved directly; each subsequent equation (moving upward) has exactly one new unknown once all later variables are substituted in, so it is solvable exactly by substitution, giving the unique correct value at each step. ∎

### Variants / Use Cases

- **Gauss-Jordan Elimination** → continues reduction to fully diagonal (reduced row echelon) form, reading off the solution directly with no back-substitution
- **LU Decomposition** → records the elimination multipliers to factor `A = LU`, reusable across multiple right-hand sides
- **Determinant computation** → product of the pivots, with a sign flip for each row swap performed
- **Rank / null-space computation** → the number of nonzero pivot rows gives the matrix rank
- **Matrix inversion** → augmenting with the identity matrix and running full (Gauss-Jordan) reduction yields the inverse