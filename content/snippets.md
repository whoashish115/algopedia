---
title: 💎 Snippets
---
## Fibonacci using Matrix Exponentiation

The Fibonacci recurrence can be written as

$$
\begin{bmatrix}
F(n)\\
F(n-1)
\end{bmatrix}
=
\begin{bmatrix}
1 & 1\\
1 & 0
\end{bmatrix}^{n-1}
\begin{bmatrix}
F(1)\\
F(0)
\end{bmatrix}.
$$

Since

$$
\begin{bmatrix}
F(1)\\
F(0)
\end{bmatrix}
=
\begin{bmatrix}
1\\
0
\end{bmatrix},
$$
After computing $M^{n-1}$, the answer is simply the element at position $(0,0)$.
```cpp
long long fibonacci(long long n) {
    if (n == 0) return 0;

    Matrix M(2);
    M.a = {
        {1, 1},
        {1, 0}
    };

    Matrix res = matrix_power(M, n - 1);
    return res.a[0][0];
}
```