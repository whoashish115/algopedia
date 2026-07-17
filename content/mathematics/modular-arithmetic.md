---
title: Modular Arithmetic
---

When numbers become very large, we perform computations modulo a fixed integer $m$, storing only the remainder. Two integers are congruent modulo $m$ if

$$
a \equiv b \pmod m
\iff
m \mid (a-b).
$$

## Basic Operations

$$
(a+b)\bmod m=((a\bmod m)+(b\bmod m))\bmod m
$$

$$
(a-b)\bmod m=((a\bmod m)-(b\bmod m)+m)\bmod m
$$

$$
(ab)\bmod m=((a\bmod m)(b\bmod m))\bmod m
$$

Division is **not** defined directly. Instead,

$$
\frac{a}{b}\bmod m
=
a\cdot b^{-1}\pmod m,
$$

where $b^{-1}$ is the modular multiplicative inverse of $b$, computed with the methods below.

## Modular Exponentiation

Computing $a^n \bmod m$ efficiently is a prerequisite for most of this note — see **Binary Exponentiation** for the $O(\log n)$ `modpow` implementation. Every function below assumes `modpow(a, n, mod)` is available.

## Modular Inverse

The modular inverse of $a$ modulo $m$ is the number $x$ satisfying

$$
ax\equiv1\pmod m.
$$

It exists **iff** $\gcd(a,m)=1$.

### Fermat's Little Theorem (Prime Modulus)

> [!abstract]- Mathematical Proof
> If $p$ is prime and $p \nmid a$, Fermat's Little Theorem states:
> $$a^{p-1}\equiv1\pmod p.$$
> Multiplying both sides by $a^{-1}$ gives
> $$a^{-1}\equiv a^{p-2}\pmod p.$$
> So raising $a$ to the power $p-2$ (via binary exponentiation) directly yields its inverse — no division needed.

$$
\frac{a}{b}\pmod p
=
a\cdot b^{p-2}\pmod p.
$$

```cpp
long long modinv(long long a, long long mod) {
    return modpow(a, mod - 2, mod); // mod must be prime
}
```

**Time Complexity:** $\mathcal{O}(\log m)$
**Space Complexity:** $\mathcal{O}(1)$

### Extended Euclidean Algorithm (Any Modulus)

Works even when $m$ is **not prime**, as long as $\gcd(a,m)=1$.

> [!abstract]- Mathematical Proof
> By Bézout's identity, the extended Euclidean algorithm finds integers $x, y$ such that
> $$ax + my = \gcd(a,m).$$
> If $\gcd(a,m)=1$, this becomes $ax + my = 1$, i.e. $ax \equiv 1 \pmod m$. Therefore $x$ (reduced mod $m$) is the modular inverse of $a$.

```cpp
long long extgcd(long long a, long long b, long long &x, long long &y) {
    if (b == 0) { x = 1; y = 0; return a; }
    long long x1, y1;
    long long g = extgcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - (a / b) * y1;
    return g;
}

long long modinv(long long a, long long mod) {
    long long x, y;
    long long g = extgcd(a, mod, x, y);
    if (g != 1) return -1; // inverse doesn't exist
    return ((x % mod) + mod) % mod;
}
```

**Algorithm:**
* Run the extended Euclidean algorithm on $(a, m)$ to obtain $\gcd(a,m)$ along with coefficients $x, y$.
* If the gcd isn't $1$, no inverse exists.
* Otherwise, reduce $x$ modulo $m$ (adding $m$ if negative) to get $a^{-1} \bmod m$.

**Time Complexity:** $\mathcal{O}(\log(\min(a, m)))$
**Space Complexity:** $\mathcal{O}(\log(\min(a, m)))$ (recursion stack)

### Inverses of $1 \dots n$ in $O(n)$

Useful when every inverse from $1$ to $n$ is needed (e.g. for factorial tables), since calling `modinv` $n$ times costs $O(n \log m)$.

$$
inv[1] = 1,
\qquad
inv[i] = -\left\lfloor \frac{m}{i} \right\rfloor \cdot inv[m \bmod i] \pmod m.
$$

> [!abstract]- Mathematical Proof
> Write $m = \lfloor m/i \rfloor \cdot i + (m \bmod i)$. Taking this equation mod $m$ gives
> $$\left\lfloor \frac{m}{i} \right\rfloor \cdot i + (m \bmod i) \equiv 0 \pmod m.$$
> Multiply both sides by $i^{-1} \cdot (m \bmod i)^{-1}$ and rearrange to isolate $i^{-1}$:
> $$i^{-1} \equiv -\left\lfloor \frac{m}{i} \right\rfloor \cdot (m \bmod i)^{-1} \pmod m.$$
> Since $m \bmod i < i$, its inverse $inv[m \bmod i]$ was already computed in an earlier iteration, so the recurrence can be built bottom-up.

```cpp
vector<long long> inverseRange(int n, long long mod) {
    vector<long long> inv(n + 1);
    inv[1] = 1;
    for (int i = 2; i <= n; i++)
        inv[i] = (mod - (mod / i) * inv[mod % i] % mod) % mod;
    return inv;
}
```

**Time Complexity:** $\mathcal{O}(n)$
**Space Complexity:** $\mathcal{O}(n)$

## Chinese Remainder Theorem (CRT)

Solves a system of congruences with pairwise coprime moduli:

$$
x \equiv r_1 \pmod{m_1}, \qquad x \equiv r_2 \pmod{m_2}, \qquad \gcd(m_1, m_2) = 1.
$$

A unique solution exists modulo $M = m_1 m_2$:

$$
x \equiv r_1 \cdot m_2 \cdot (m_2^{-1} \bmod m_1) \;+\; r_2 \cdot m_1 \cdot (m_1^{-1} \bmod m_2) \pmod M.
$$

```cpp
long long crt(long long r1, long long m1, long long r2, long long m2) {
    long long M = m1 * m2;
    long long inv1 = modinv(m2 % m1, m1); // extended-Euclid version, since m1 may not be prime
    long long inv2 = modinv(m1 % m2, m2);
    long long x = (r1 % M) * m2 % M * inv1 % M;
    x = (x + (r2 % M) * m1 % M * inv2 % M) % M;
    return (x + M) % M;
}
```

**Algorithm:**
* Compute $M = m_1 \cdot m_2$.
* Find the inverse of $m_2$ mod $m_1$, and the inverse of $m_1$ mod $m_2$, using the extended Euclidean algorithm.
* Combine the two congruences into a single residue mod $M$ using the formula above.
* For more than two congruences, merge them pairwise, folding the result into a running $(r, m)$ pair.

**Time Complexity:** $\mathcal{O}(\log(\min(m_1, m_2)))$ per merge
**Space Complexity:** $\mathcal{O}(1)$

## Combinatorics Under a Prime Modulus

Precomputing factorials and inverse factorials gives $O(1)$ queries for $\binom{n}{r} \bmod p$.

$$
\binom{n}{r} \bmod p = fact[n] \cdot invfact[r] \cdot invfact[n-r] \bmod p.
$$

```cpp
const int MAXN = 200005;
long long fact[MAXN], invfact[MAXN];

void precomputeFactorials(long long mod) {
    fact[0] = 1;
    for (int i = 1; i < MAXN; i++)
        fact[i] = fact[i - 1] * i % mod;

    invfact[MAXN - 1] = modpow(fact[MAXN - 1], mod - 2, mod);
    for (int i = MAXN - 2; i >= 0; i--)
        invfact[i] = invfact[i + 1] * (i + 1) % mod;
}

long long nCr(int n, int r, long long mod) {
    if (r < 0 || r > n) return 0;
    return fact[n] * invfact[r] % mod * invfact[n - r] % mod;
}
```

**Algorithm:**
* Build `fact[i] = i! mod p` iteratively from $0$ to `MAXN - 1`.
* Compute `invfact[MAXN - 1]` once via Fermat's Little Theorem, then fill the rest **backwards** using $invfact[i] = invfact[i+1] \cdot (i+1) \bmod p$, avoiding $\mathcal{O}(\log p)$ work per entry.
* Answer each $\binom{n}{r}$ query in $O(1)$ using the precomputed arrays.

**Time Complexity:** $\mathcal{O}(N)$ precompute, $\mathcal{O}(1)$ per query
**Space Complexity:** $\mathcal{O}(N)$

## Lucas' Theorem

Computes $\binom{n}{r} \bmod p$ when $n, r$ can be far larger than $p$, but $p$ itself is a small prime (too small to precompute factorials up to $n$).

$$
\binom{n}{r} \bmod p = \prod_{i} \binom{n_i}{r_i} \bmod p,
$$

where $n_i, r_i$ are the base-$p$ digits of $n$ and $r$.

```cpp
long long smallNCr(long long n, long long r, long long p) {
    if (r < 0 || r > n) return 0;
    long long res = 1;
    for (long long i = 0; i < r; i++)
        res = res * ((n - i) % p) % p * modinv((i + 1) % p, p) % p;
    return res;
}

long long lucas(long long n, long long r, long long p) {
    if (r == 0) return 1;
    return smallNCr(n % p, r % p, p) * lucas(n / p, r / p, p) % p;
}
```

**Algorithm:**
* Peel off the last base-$p$ digit of $n$ and $r$, compute $\binom{n_i}{r_i} \bmod p$ directly (both digits are $< p$, so this is a small, direct computation).
* Recurse on $n / p$ and $r / p$ to handle the remaining digits.
* Multiply all digit-wise binomial coefficients together mod $p$; if any $r_i > n_i$, the whole product is $0$.

**Time Complexity:** $\mathcal{O}(p \log_p n)$
**Space Complexity:** $\mathcal{O}(\log_p n)$ (recursion stack)

## Common Moduli

| Modulus | Notes |
|---------:|-------|
| `1000000007` | Standard prime modulus for most problems |
| `998244353` | Prime modulus commonly used for NTT |
| `1000000009` | Another commonly used prime |
