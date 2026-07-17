---
title: Matrix Exponentiation
---

Matrix exponentiation is an extension of binary exponentiation that computes the power of a square matrix $M^n$ in **$O(k^3\log n)$** time, where $k$ is the size of the matrix. Instead of multiplying the matrix by itself $n$ times, it repeatedly **squares the matrix** and **halves the exponent**, exactly like scalar binary exponentiation. It is commonly used for **linear recurrences** and **state transition** problems, where each state is obtained by applying the same transformation matrix to the previous state.

Suppose the state of a system is represented by a vector $S(n)$, and the transition between consecutive states is given by a fixed matrix $M$:

$$
S(n+1)=M\cdot S(n).
$$

Applying the transition repeatedly gives

$$
S(n)=M^n\cdot S(0).
$$

Just like binary exponentiation decomposes an exponent into powers of two,

$$
13 = 1101_2 = 2^3 + 2^2 + 2^0 = 8 + 4 + 1,
$$

matrix exponentiation computes

$$
M^{13}
=
M^{8+4+1}
=
M^8\cdot M^4\cdot M^1.
$$

Thus, matrix exponentiation only multiplies the matrix powers corresponding to the set (`1`) bits in the binary representation of the exponent.

## Implementation

Matrix exponentiation is implemented in two parts. First, we need a **matrix multiplication** function, where each element of the resulting matrix is computed as the dot product of the corresponding row of the first matrix and column of the second matrix:

$$
(AB)_{ij}=\sum_{k=0}^{n-1}A_{ik}B_{kj}.
$$

After that, binary exponentiation is applied to matrices. The answer is initialized as the **identity matrix** $I$, which plays the same role as `1` in ordinary binary exponentiation. Whenever the current bit of the exponent is `1`, the current matrix power is multiplied into the answer. After every iteration, the matrix is squared and the exponent is halved by shifting it one bit to the right.

```cpp
struct Matrix {
    int n;
    vector<vector<long long>> a;

    Matrix(int n = 0) {
        this->n = n;
        a.assign(n, vector<long long>(n, 0));
    }
};

Matrix multiply(const Matrix &A, const Matrix &B) {
    int n = A.n;
    Matrix C(n);

    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            for (int k = 0; k < n; k++)
                C.a[i][j] = (C.a[i][j] + A.a[i][k] * B.a[k][j]) % MOD;

    return C;
}

Matrix matrix_power(Matrix base, long long exp) {
    int n = base.n;
    Matrix res(n);

    for (int i = 0; i < n; i++)
        res.a[i][i] = 1;

    while (exp > 0) {
        if (exp & 1)
            res = multiply(res, base);

        base = multiply(base, base);
        exp >>= 1;
    }

    return res;
}
```