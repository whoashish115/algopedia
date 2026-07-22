---
title: Lucas' Theorem
---

**Purpose:** Compute binomial coefficients `C(n, k) mod p` for a prime `p`, even when `n` and `k` are far too large to compute `C(n,k)` directly, in O(log_p n) time.
### Algorithm
1. Write `n` and `k` in base `p`: `n = n_m n_(m-1) ... n_1 n_0` and `k = k_m k_(m-1) ... k_1 k_0` (digits in base `p`, padding with leading zeros as needed).
2. Lucas' theorem states: `C(n, k) mod p = Π C(n_i, k_i) mod p` over all corresponding base-`p` digit pairs.
3. If any `k_i > n_i` for some digit position, that digit's `C(n_i, k_i) = 0`, making the entire product 0 — meaning `C(n, k) ≡ 0 (mod p)`.
4. Compute each small `C(n_i, k_i) mod p` directly (since digits are in `[0, p-1]`, this is a small, direct computation via a precomputed factorial table mod `p`).
5. Multiply all digit-wise results together modulo `p` to get the final answer.

### Code
```cpp
long long powmod(long long a, long long e, long long m) {
    long long r = 1; a %= m;
    while (e) { if (e & 1) r = r * a % m; a = a * a % m; e >>= 1; }
    return r;
}

long long smallComb(long long n, long long k, long long p, vector<long long>& fact) {
    if (k < 0 || k > n) return 0;
    return fact[n] * powmod(fact[k], p - 2, p) % p * powmod(fact[n - k], p - 2, p) % p;
}

long long lucasTheorem(long long n, long long k, long long p) {
    vector<long long> fact(p);
    fact[0] = 1;
    for (int i = 1; i < p; i++) fact[i] = fact[i - 1] * i % p;

    long long result = 1;
    while (n > 0 || k > 0) {
        long long ni = n % p, ki = k % p;
        result = result * smallComb(ni, ki, p, fact) % p;
        n /= p; k /= p;
    }
    return result;
}
```

### Paradigm
**Transform and Conquer.** The core idea is instance simplification: transforming an intractably large binomial coefficient computation into a product of small, directly computable binomial coefficients on individual base-`p` digits.

### Complexity
- Time: O(p) preprocessing for factorials, O(log_p n) per query
- Space: O(p)

### Proof of Correctness
**Core identity (Lucas' theorem):** For a prime `p` and non-negative integers `n = Σ n_i p^i`, `k = Σ k_i p^i` (base-`p` digit expansions), `C(n, k) ≡ Π C(n_i, k_i) (mod p)`.

**Proof sketch via generating functions:** Consider `(1 + x)^n mod p` as a polynomial over `Z/pZ`. By the Freshman's Dream (a consequence of the Binomial Theorem combined with Fermat's Little Theorem in characteristic `p`), `(1 + x)^p ≡ 1 + x^p (mod p)`. Applying this repeatedly:
```
(1 + x)^n = (1 + x)^(Σ n_i p^i) = Π (1 + x)^(n_i p^i) = Π ((1+x)^(p^i))^(n_i) ≡ Π (1 + x^(p^i))^(n_i) (mod p)
```
Expanding the right-hand side, the coefficient of `x^k` (where `k = Σ k_i p^i`) is obtained by choosing, for each digit position `i`, exactly `k_i` copies of `x^(p^i)` out of the `n_i` available "slots" `(1 + x^(p^i))^(n_i)` — contributing a factor of `C(n_i, k_i)` per digit. Multiplying across all digit positions gives exactly `Π C(n_i, k_i)`, which by matching coefficients with the left-hand side `(1+x)^n`'s coefficient of `x^k` (namely `C(n, k)`) proves the identity modulo `p`.

**Why digit-wise coefficients can be computed directly:** Since each digit `n_i, k_i ∈ [0, p-1]`, `C(n_i, k_i)` is a small binomial coefficient computable directly via precomputed factorials modulo `p` (using modular inverses, since `p` is prime and all values less than `p` are invertible modulo `p`). ∎

### Variants / Use Cases
- **Kummer's theorem** → related result determining the exact power of `p` dividing `C(n,k)`, useful for composite or prime-power moduli
- **Binomial coefficients modulo prime powers** → generalizations (e.g., Andrew Granville's work) extend Lucas' theorem beyond a single prime
- **Combinatorics competition problems** → common technique for computing `C(n,k) mod p` when `n, k` are astronomically large
- **Counting lattice paths modulo a prime** → binomial coefficients modulo p arise naturally in combinatorial path-counting problems
- **Modular Pascal's Triangle structure analysis** → Lucas' theorem explains Sierpinski-triangle-like patterns seen in Pascal's triangle mod small primes
