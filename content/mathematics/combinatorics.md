---
title: Combinatorics
---

## Permutations

$$
P(n) = n! \qquad \text{(arrange all } n \text{ distinct objects)}
$$

$$
P(n, r) = \frac{n!}{(n-r)!} \qquad \text{(arrange } r \text{ of } n \text{ distinct objects, order matters)}
$$

## Combinations

$$
\binom{n}{r} = \frac{n!}{r!\,(n-r)!} \qquad \text{(choose } r \text{ of } n \text{ distinct objects, order doesn't matter)}
$$

Also written $nCr$.

**Pascal's identity:**

$$
\binom{n}{r} = \binom{n-1}{r-1} + \binom{n-1}{r}.
$$

Basis of Pascal's triangle; gives an $O(n^2)$ DP for binomial coefficients without factorials or modular inverses.

**Identities:**

$$
\binom{n}{0} = \binom{n}{n} = 1, \qquad \binom{n}{r} = \binom{n}{n-r}, \qquad \sum_{r=0}^{n}\binom{n}{r} = 2^n.
$$

### Binomial Theorem

$$
(x+y)^n = \sum_{r=0}^{n} \binom{n}{r}\, x^{n-r} y^r.
$$

## Computing nCr Fast, Modulo a Prime

For $n$ up to $10^6$-$10^7$: precompute factorials and inverse factorials mod $\text{MOD}$, then answer each query in $O(1)$. (See **Modular Arithmetic** for the standalone `modinv` / `modpow` building blocks.)

$$
fact[0] = 1, \qquad fact[i] = fact[i-1]\cdot i \bmod \text{MOD}.
$$

$$
invfact[N] = fact[N]^{\text{MOD}-2} \bmod \text{MOD} \ \ \text{(Fermat's Little Theorem)}, \qquad invfact[i-1] = invfact[i]\cdot i \bmod \text{MOD}.
$$

$$
\binom{n}{r} \bmod \text{MOD} = fact[n]\cdot invfact[r]\cdot invfact[n-r] \bmod \text{MOD}.
$$

### Code — Factorial / Inverse-Factorial Precomputation

```cpp
#include <bits/stdc++.h>
using namespace std;

static const long long MOD = 1000000007;
static const int MAXN = 1000000;

long long fact[MAXN + 1], invfact[MAXN + 1];

long long modpow(long long a, long long b, long long mod) {
    long long res = 1;
    a %= mod;
    while (b > 0) {
        if (b & 1) res = res * a % mod;
        a = a * a % mod;
        b >>= 1;
    }
    return res;
}

void precompute() {
    fact[0] = 1;
    for (int i = 1; i <= MAXN; i++)
        fact[i] = fact[i - 1] * i % MOD;

    invfact[MAXN] = modpow(fact[MAXN], MOD - 2, MOD);
    for (int i = MAXN; i > 0; i--)
        invfact[i - 1] = invfact[i] * i % MOD;
}

long long nCr(int n, int r) {
    if (r < 0 || r > n) return 0;
    return fact[n] * invfact[r] % MOD * invfact[n - r] % MOD;
}
```

### Code — Pascal's-Triangle DP (Small n, No Modular Inverse Needed)

```cpp
vector<vector<long long>> pascal(int n, long long mod) {
    vector<vector<long long>> C(n + 1, vector<long long>(n + 1, 0));
    for (int i = 0; i <= n; i++) {
        C[i][0] = 1;
        for (int j = 1; j <= i; j++)
            C[i][j] = (C[i - 1][j - 1] + C[i - 1][j]) % mod;
    }
    return C;
}
```

## Catalan Numbers

$$
C_n = \frac{1}{n+1}\binom{2n}{n} = \binom{2n}{n} - \binom{2n}{n+1}, \qquad C_0 = 1.
$$

$$
C_{n+1} = \sum_{i=0}^{n} C_i \, C_{n-i}.
$$

> [!abstract]- Mathematical Proof
> The closed form is derived with the **reflection principle**: count lattice paths from $(0,0)$ to $(n,n)$ using unit right/up steps that never cross above the diagonal. The total number of monotone paths is $\binom{2n}{n}$. Reflecting any "bad" path (one that touches the line $y=x+1$) across that line maps it bijectively onto an unconstrained path to $(n-1, n+1)$, of which there are $\binom{2n}{n+1}$. Subtracting the bad paths leaves $\binom{2n}{n}-\binom{2n}{n+1}$ valid paths, which simplifies to $\frac{1}{n+1}\binom{2n}{n}$.

Catalan numbers count balanced parenthesizations, binary trees with $n$ internal nodes, Dyck paths, and polygon triangulations, among many other structures.

```cpp
long long catalan(int n, long long mod) {
    // requires precompute() from the nCr section above
    return ((nCr(2 * n, n) - nCr(2 * n, n + 1)) % mod + mod) % mod;
}
```

**Time Complexity:** $\mathcal{O}(1)$ per query (given precomputed factorials), $\mathcal{O}(N)$ precompute

## Stars and Bars

Number of ways to distribute $n$ identical items into $k$ distinguishable bins.

**Empty bins allowed:**

$$
\binom{n+k-1}{k-1}.
$$

**Each bin non-empty:**

$$
\binom{n-1}{k-1}.
$$

> [!abstract]- Mathematical Proof
> Represent a distribution as a row of $n$ stars (items) separated by $k-1$ bars (bin boundaries). Any arrangement of these $n+k-1$ symbols is a valid distribution, and it's fully determined by choosing which $k-1$ of the $n+k-1$ positions hold bars — hence $\binom{n+k-1}{k-1}$. For the non-empty case, first place one item in every bin, then distribute the remaining $n-k$ items freely, giving $\binom{(n-k)+k-1}{k-1} = \binom{n-1}{k-1}$.

```cpp
long long distributeItems(int n, int k, bool allowEmpty) {
    if (allowEmpty) return nCr(n + k - 1, k - 1);
    return nCr(n - 1, k - 1); // n must be >= k
}
```

## Inclusion-Exclusion Principle

$$
\left|\bigcup_{i=1}^{n} A_i\right|
=
\sum_{i} |A_i|
- \sum_{i<j} |A_i \cap A_j|
+ \sum_{i<j<l} |A_i \cap A_j \cap A_l|
- \dots
+ (-1)^{n+1}\left|A_1 \cap \dots \cap A_n\right|.
$$

Standard applications: counting numbers divisible by at least one of a set of primes, counting surjective functions, Euler's totient (see **General**), and derangements (below).

```cpp
// counts integers in [1, N] divisible by at least one of the given primes
long long countDivisibleByAny(long long N, vector<long long> &primes) {
    int k = primes.size();
    long long total = 0;
    for (int mask = 1; mask < (1 << k); mask++) {
        long long lcm = 1;
        int bits = __builtin_popcount(mask);
        bool overflow = false;
        for (int i = 0; i < k; i++) {
            if (mask & (1 << i)) {
                long long g = __gcd(lcm, primes[i]);
                if (lcm / g > N / primes[i]) { overflow = true; break; }
                lcm = lcm / g * primes[i];
            }
        }
        if (overflow) continue;
        long long term = N / lcm;
        total += (bits % 2 == 1) ? term : -term;
    }
    return total;
}
```

**Time Complexity:** $\mathcal{O}(2^k \cdot k)$ for $k$ sets

## Derangements

Permutations of $n$ elements with **no fixed points**.

$$
D_n = n! \sum_{i=0}^{n} \frac{(-1)^i}{i!}.
$$

$$
D_n = (n-1)\big(D_{n-1} + D_{n-2}\big), \qquad D_0 = 1,\ D_1 = 0.
$$

> [!abstract]- Mathematical Proof
> Applying Inclusion-Exclusion to the events "$i$ is a fixed point" over all $n$ positions gives $D_n = \sum_{i=0}^n (-1)^i \binom{n}{i}(n-i)! = n!\sum_{i=0}^n \frac{(-1)^i}{i!}$.

```cpp
long long derangements(int n, long long mod) {
    vector<long long> D(n + 1);
    D[0] = 1;
    if (n >= 1) D[1] = 0;
    for (int i = 2; i <= n; i++)
        D[i] = (i - 1) * ((D[i - 1] + D[i - 2]) % mod) % mod;
    return D[n];
}
```

**Time Complexity:** $\mathcal{O}(n)$

## Burnside's Lemma

Counts the number of distinct objects up to the symmetry of a group $G$ acting on a set $X$:

$$
|X/G| = \frac{1}{|G|}\sum_{g \in G} |X^g|,
$$

where $X^g$ is the set of elements of $X$ fixed by $g$.

**Classic example — necklaces:** count distinct necklaces of $n$ beads and $k$ colors under rotation. A rotation by $d$ positions decomposes the $n$ beads into $\gcd(n,d)$ cycles, and a coloring is fixed by that rotation iff it's constant on every cycle — giving $k^{\gcd(n,d)}$ fixed colorings.

```cpp
long long distinctNecklaces(int n, int k, long long mod) {
    long long total = 0;
    for (int d = 0; d < n; d++) {
        int g = __gcd(n, d);
        total = (total + modpow(k, g, mod)) % mod;
    }
    return total * modinv(n, mod) % mod; // modinv from Modular Arithmetic
}
```

**Time Complexity:** $\mathcal{O}(n \log(\text{mod}))$

## Pólya Enumeration Theorem

A restatement of Burnside's Lemma specialized to **coloring** problems: since a coloring is fixed by $g$ exactly when it's constant on every cycle of $g$, $|X^g| = k^{c(g)}$ where $c(g)$ is the number of cycles in $g$'s permutation of the underlying positions. This gives

$$
|X/G| = \frac{1}{|G|}\sum_{g\in G} k^{\,c(g)}.
$$

The necklace-counting code above is already an instance of PET: rotation by $d$ decomposes $n$ positions into $c(g) = \gcd(n,d)$ cycles. PET becomes essential (over plain Burnside) once the group $G$ includes more complex symmetries (e.g. reflections, or automorphisms of a graph), where $c(g)$ must be computed from the specific permutation structure of each $g$ rather than a simple $\gcd$.

## Generating Functions

**Ordinary Generating Function (OGF)** of a sequence $a_0, a_1, a_2, \dots$:

$$
A(x) = \sum_{n \ge 0} a_n x^n.
$$

Sequence operations become algebraic operations on the generating function: shifting the sequence corresponds to multiplying by $x$, and **convolution** ($c_n = \sum_i a_i b_{n-i}$) corresponds to multiplying the two generating functions, $C(x) = A(x)B(x)$.

Examples:

$$
\text{Fibonacci: } F(x) = \frac{x}{1-x-x^2}, \qquad
\text{Catalan: } C(x) = \frac{1-\sqrt{1-4x}}{2x}, \qquad
\text{Stars-and-bars (}k\text{ bins): } \left(\frac{1}{1-x}\right)^k.
$$

**Exponential Generating Function (EGF)**, used for **labeled** structures:

$$
A(x) = \sum_{n \ge 0} a_n \frac{x^n}{n!}.
$$

Example: the EGF for permutations of $n$ labeled items is $\frac{1}{1-x}$; the EGF for derangements is $\frac{e^{-x}}{1-x}$.

In competitive programming, generating functions are mainly wielded through **polynomial multiplication (convolution)**, often accelerated with NTT (see the `998244353` modulus in **Modular Arithmetic**), to compute an entire sequence's terms in bulk rather than one value at a time.

## Stirling Numbers

**First kind** $\left[{n \atop k}\right]$ — number of permutations of $n$ elements with exactly $k$ cycles:

$$
\left[{n \atop k}\right] = \left[{n-1 \atop k-1}\right] + (n-1)\left[{n-1 \atop k}\right].
$$

**Second kind** $\left\{{n \atop k}\right\}$ — number of ways to partition a set of $n$ labeled elements into exactly $k$ non-empty subsets:

$$
\left\{{n \atop k}\right\} = \left\{{n-1 \atop k-1}\right\} + k\left\{{n-1 \atop k}\right\}.
$$

```cpp
vector<vector<long long>> stirlingSecondKind(int n, long long mod) {
    vector<vector<long long>> S(n + 1, vector<long long>(n + 1, 0));
    S[0][0] = 1;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= i; j++)
            S[i][j] = (S[i - 1][j - 1] + 1LL * j * S[i - 1][j]) % mod;
    return S;
}
```

**Time Complexity:** $\mathcal{O}(n^2)$

## Bell Numbers

Total number of ways to partition a set of $n$ labeled elements into **any** number of non-empty subsets:

$$
B_n = \sum_{k=0}^{n} \left\{{n \atop k}\right\}.
$$

Computed directly with the **Bell triangle**, avoiding the need to build the full Stirling table:

$$
a_{i,0} = a_{i-1,\,i-1}, \qquad a_{i,j} = a_{i,j-1} + a_{i-1,j-1}, \qquad B_n = a_{n,0}.
$$

```cpp
vector<long long> bellNumbers(int n, long long mod) {
    vector<vector<long long>> tri(n + 1);
    tri[0] = {1};
    vector<long long> B(n + 1);
    B[0] = 1;
    for (int i = 1; i <= n; i++) {
        tri[i].assign(i + 1, 0);
        tri[i][0] = tri[i - 1][i - 1];
        for (int j = 1; j <= i; j++)
            tri[i][j] = (tri[i][j - 1] + tri[i - 1][j - 1]) % mod;
        B[i] = tri[i][0];
    }
    return B;
}
```

**Time Complexity:** $\mathcal{O}(n^2)$

## Partition Numbers

The integer partition function $p(n)$ counts the number of ways to write $n$ as a sum of positive integers, **ignoring order**.

$$
\sum_{n \ge 0} p(n)\, x^n = \prod_{k=1}^{\infty} \frac{1}{1-x^k}.
$$

Computed with a coin-change-style DP that builds every value up to $n$ using parts $1 \dots n$:

```cpp
vector<long long> partitionNumbers(int n, long long mod) {
    vector<long long> p(n + 1, 0);
    p[0] = 1;
    for (int part = 1; part <= n; part++)
        for (int i = part; i <= n; i++)
            p[i] = (p[i] + p[i - part]) % mod;
    return p;
}
```

**Time Complexity:** $\mathcal{O}(n^2)$
**Space Complexity:** $\mathcal{O}(n)$

## Recurrence Relations

A **linear homogeneous recurrence with constant coefficients** has the form

$$
a_n = c_1 a_{n-1} + c_2 a_{n-2} + \dots + c_k a_{n-k}.
$$

Its behavior is governed by the **characteristic equation**

$$
x^k = c_1 x^{k-1} + c_2 x^{k-2} + \dots + c_k,
$$

whose roots $x_1, \dots, x_k$ give the closed form $a_n = \sum_i \alpha_i x_i^n$ (for distinct roots), with the $\alpha_i$ fixed by the initial conditions.

For competitive programming, the practical takeaway is that **any such recurrence can be evaluated for huge $n$ in $\mathcal{O}(k^3 \log n)$** by encoding one step of the recurrence as a $k \times k$ transition matrix and applying **Matrix Exponentiation**. For example, Fibonacci's $a_n = a_{n-1} + a_{n-2}$ becomes

$$
\begin{pmatrix} a_{n+1} \\ a_n \end{pmatrix}
=
\begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}
\begin{pmatrix} a_n \\ a_{n-1} \end{pmatrix}.
$$

## Pigeonhole Principle

**Naive form.** If more than $n$ objects are placed into $n$ boxes, at least one box contains more than one object.

**General form.** If $p$ pigeons are placed into $k$ holes, at least one hole contains at least $\lceil p/k \rceil$ pigeons.

**Generalized form.** If more than $k \cdot n$ objects are placed into $n$ boxes, at least one box contains more than $k$ objects.

Application is usually a two-step process: identify what the "pigeons" and "holes" are, then finish with problem-specific reasoning once the principle guarantees a collision.
