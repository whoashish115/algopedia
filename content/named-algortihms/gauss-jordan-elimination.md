---
title: Gauss jordan elimination
---

**Purpose:** Solve linear systems or compute a matrix inverse by reducing the augmented matrix all the way to reduced row echelon form (RREF), in O(n^3) time.

### Algorithm

1. Augment the matrix `A` with `b` (or with the identity matrix, if computing an inverse).
2. For each column, select the row with the largest-magnitude entry as the pivot row (for numerical stability) and swap it into position.
3. Normalize the pivot row by dividing every entry by the pivot value, so the pivot becomes exactly 1.
4. Eliminate that column's entries in _every other row_ — both above and below the pivot, not just below.
5. Repeat for each column in turn, until the left-hand side is a full identity matrix.
6. The augmented columns on the right now directly contain the solution vector (or the matrix inverse).

### Code

```cpp
vector<vector<double>> gaussJordan(vector<vector<double>> a) {
    int n = a.size(), m = a[0].size();
    for (int col = 0; col < n; col++) {
        int piv = col;
        for (int row = col + 1; row < n; row++)
            if (fabs(a[row][col]) > fabs(a[piv][col])) piv = row;
        swap(a[col], a[piv]);
        if (fabs(a[col][col]) < 1e-9) continue;

        double pivotVal = a[col][col];
        for (int c = 0; c < m; c++) a[col][c] /= pivotVal;

        for (int row = 0; row < n; row++) {
            if (row == col) continue;
            double factor = a[row][col];
            for (int c = 0; c < m; c++) a[row][c] -= factor * a[col][c];
        }
    }
    return a;
}
```

### Paradigm

**Decrease and Conquer.** Like Gaussian elimination, each pivot step reduces the effective size of the unsolved problem, but here each pivot fully clears its column (above and below) immediately, avoiding a separate back-substitution phase at the end.

### Complexity

- Time: O(n^3)
- Space: O(n · m), where `m` is the augmented width (`n+1` for a system, `2n` for an inverse)

### Proof of Correctness

Each step is an elementary row operation (scaling, swapping, adding a multiple of one row to another), which preserves the solution set / row space of the system, exactly as in Gaussian elimination.

**Invariant:** after processing column `k`, column `k` contains a single 1 in row `k` and 0 everywhere else, and this pattern is preserved for all previously-processed columns. This holds by induction: eliminating column `k`'s entries in other rows only adds multiples of the pivot row (which already has 0s in all previously-cleared columns except its own pivot) to those rows, so no zero entry established earlier is disturbed.

After all `n` columns are processed, the left block is exactly the identity matrix by the invariant, so the right block must equal the unique solution to `Ax = b` (or the matrix inverse, if augmented with `I`), since the transformed system `Ix = x` is equivalent to the original. ∎

### Variants / Use Cases

- **Matrix inversion** → augment with the identity matrix, read off the right block after reduction
- **Multiple right-hand sides** → augment with several `b` vectors at once and solve them all in a single reduction
- **Rank / null-space basis** → RREF directly exposes pivot and free columns
- **Simplex Method (Linear Programming)** → pivoting operations directly generalize Gauss-Jordan-style row reduction
- **Modular linear algebra (over GF(2) or GF(p))** → used in coding theory and cryptographic algorithms