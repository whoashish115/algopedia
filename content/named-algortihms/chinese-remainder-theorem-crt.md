---
title: Chinese Remainder Theorem (CRT)
---

**Purpose:** Solve a system of simultaneous modular congruences `x ≡ a_i (mod m_i)` for pairwise coprime moduli, finding a unique solution modulo the product of all moduli, in O(k log M) time for `k` congruences with product `M`.

### Algorithm
1. Given congruences `x ≡ a1 (mod m1)`, `x ≡ a2 (mod m2)`, ..., with all `mi` pairwise coprime.
2. Compute `M = m1 · m2 · ... · mk` (the product of all moduli).
3. For each `i`, compute `Mi = M / mi` and its modular inverse `Mi_inv = Mi⁻¹ mod mi` (via the Extended Euclidean Algorithm).
4. The solution is `x = Σ (ai · Mi · Mi_inv) mod M`.
5. This `x` is the unique solution modulo `M`.

### Code
```cpp
long long extendedGCD(long long a, long long b, long long& x, long long& y) {
    if (b == 0) { x = 1; y = 0; return a; }
    long long x1, y1;
    long long g = extendedGCD(b, a % b, x1, y1);
    x = y1; y = x1 - (a / b) * y1;
    return g;
}

long long modInverse(long long a, long long m) {
    long long x, y;
    extendedGCD(a, m, x, y);
    return ((x % m) + m) % m;
}

long long crt(vector<long long>& a, vector<long long>& m) {
    long long M = 1;
    for (long long mi : m) M *= mi;

    long long result = 0;
    for (size_t i = 0; i < a.size(); i++) {
        long long Mi = M / m[i];
        long long MiInv = modInverse(Mi, m[i]);
        result = (result + (__int128)a[i] * Mi % M * MiInv) % M;
    }
    return (result + M) % M;
}
```

### Paradigm
**Transform and Conquer.** The system of congruences is transformed into a weighted sum of independently solved single-modulus components (each isolated using `Mi` and its inverse), directly combining them into one solution rather than searching or iterating.

### Complexity
- Time: O(k log M) for `k` congruences (dominated by the modular inverse computations)
- Space: O(k)

### Proof of Correctness
**Why the constructed `x` satisfies every congruence:** Fix some index `j`. For every `i ≠ j`, the term `ai · Mi · Mi_inv` is divisible by `mj`, since `Mi = M / mi` includes `mj` as a factor whenever `i ≠ j` (because all moduli are pairwise coprime, `mj` divides `Mi` for every `i ≠ j`). So modulo `mj`, only the `i = j` term survives: `x ≡ aj · Mj · Mj_inv (mod mj)`. Since `Mj_inv` is defined as the modular inverse of `Mj` modulo `mj`, `Mj · Mj_inv ≡ 1 (mod mj)`, giving `x ≡ aj · 1 = aj (mod mj)` — exactly satisfying the `j`-th congruence. This holds for every `j`, so `x` satisfies all congruences simultaneously.

**Why the solution is unique modulo `M`:** Suppose `x` and `x'` both satisfy all the congruences. Then `x - x' ≡ 0 (mod mi)` for every `i`, meaning every `mi` divides `x - x'`. Since the `mi` are pairwise coprime, their product `M` must also divide `x - x'` (a standard consequence of coprimality: pairwise coprime divisors of a number multiply to also divide it). So `x ≡ x' (mod M)`, proving uniqueness modulo `M`.

**Why a solution always exists (existence):** The explicit construction above directly produces one, so existence is constructive, not just asserted — combined with uniqueness, this fully characterizes the solution set as exactly one residue class modulo `M`. ∎

### Variants / Use Cases
- **Garner's Algorithm** → an alternative, often more numerically stable, incremental method for combining congruences without computing the full product `M` upfront until necessary
- **RSA-CRT optimization** → speeds up RSA decryption by working modulo the two prime factors separately and recombining via CRT
- **Combining sieve results across small primes** → used in number-theoretic algorithms that decompose problems modulo several small primes then reconstruct
- **Polynomial interpolation / multi-modulus computation** → used in computer algebra systems to compute exact results using multiple modular computations to avoid overflow
- **Solving simultaneous scheduling/cyclic constraints** → classic puzzle framing ("this number leaves remainder r1 when divided by m1...")
