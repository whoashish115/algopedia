---
title: Kitamasa's Algorithm
---

**Purpose:** Compute the n-th term of a linear recurrence of order `k`, in O(k² log n) time — far faster than O(n) naive iteration when `n` is huge.

### Algorithm
1. Given a linear recurrence `a_i = c1·a_(i-1) + c2·a_(i-2) + ... + ck·a_(i-k)` and initial terms `a_0, ..., a_(k-1)`.
2. Represent the recurrence as a characteristic polynomial `f(x) = x^k - c1·x^(k-1) - ... - ck`.
3. To find `a_n`, compute `x^n mod f(x)` using fast polynomial exponentiation (repeated squaring, with polynomial multiplication followed by reduction modulo `f(x)` at each step).
4. This reduction yields a polynomial `r(x) = r0 + r1·x + ... + r(k-1)·x^(k-1)` of degree less than `k`, satisfying `x^n ≡ r(x) (mod f(x))`.
5. The answer is `a_n = r0·a_0 + r1·a_1 + ... + r(k-1)·a_(k-1)`, since the recurrence structure guarantees this linear combination correctly evaluates the shifted term.

### Code
```cpp
typedef vector<long long> Poly;
const long long MOD = 1e9 + 7;

Poly polyMulMod(Poly a, Poly b, Poly& f, int k) {
    Poly res(a.size() + b.size() - 1, 0);
    for (size_t i = 0; i < a.size(); i++)
        for (size_t j = 0; j < b.size(); j++)
            res[i + j] = (res[i + j] + a[i] * b[j]) % MOD;

    for (int i = (int)res.size() - 1; i >= k; i--) {
        if (res[i] == 0) continue;
        long long coef = res[i];
        for (int j = 0; j < k; j++)
            res[i - k + j] = (res[i - k + j] - coef * f[j] % MOD + MOD) % MOD;
    }
    res.resize(k);
    return res;
}

long long kitamasa(vector<long long>& c, vector<long long>& a0, long long n) {
    int k = c.size();
    if (n < k) return a0[n];

    Poly f(k + 1); // f(x) = x^k - c1 x^(k-1) - ... - ck  (f[k]=1 implicit, stored separately)
    for (int i = 0; i < k; i++) f[i] = (MOD - c[k - 1 - i]) % MOD;
    f[k] = 1;

    Poly result = {1}, base = {0, 1};
    while (n > 0) {
        if (n & 1) result = polyMulMod(result, base, f, k);
        base = polyMulMod(base, base, f, k);
        n >>= 1;
    }
    result.resize(k, 0);

    long long ans = 0;
    for (int i = 0; i < k; i++) ans = (ans + result[i] * a0[i]) % MOD;
    return ans;
}
```

### Paradigm
**Divide and Conquer (fast exponentiation via repeated squaring).** Computing `x^n mod f(x)` uses the same halving structure as fast integer exponentiation, splitting the exponent in half each round and combining via polynomial multiplication.

### Complexity
- Time: O(k² log n) using naive polynomial multiplication (O(k log k log n) with FFT-based multiplication)
- Space: O(k)

### Proof of Correctness
**Why the recurrence corresponds to `x^k ≡ c1·x^(k-1) + ... + ck (mod f(x))`:** By construction, `f(x) = x^k - c1 x^(k-1) - ... - ck`, so `f(x) ≡ 0` directly encodes the recurrence relation as a polynomial identity: any power `x^m` satisfies `x^m ≡ c1·x^(m-1) + ... + ck·x^(m-k) (mod f(x))` for `m ≥ k`, mirroring exactly how `a_m = c1·a_(m-1) + ... + ck·a_(m-k)` behaves.

**Why `x^n mod f(x)`'s coefficients directly give `a_n` via the initial terms:** Since reduction modulo `f(x)` repeatedly applies the *same* substitution rule that defines the sequence's recurrence, the sequence of coefficients tracked through repeated reduction evolves in lockstep with how `a_n` itself would evolve under the recurrence, when "seeded" with the basis `1, x, x², ..., x^(k-1)` corresponding to `a_0, ..., a_(k-1)`. Formally, one can show by induction on `n` that if `x^n ≡ Σ ri·x^i (mod f(x))`, then `a_n = Σ ri·ai` — true trivially for `n < k` (where `x^n` needs no reduction, giving `r_n = 1` and all others 0, correctly returning `a_n` itself), and preserved inductively through each multiply-and-reduce step because reduction applies exactly the recurrence's linear substitution rule.

**Why repeated squaring computes `x^n mod f(x)` correctly and efficiently:** This is the standard fast exponentiation argument: splitting `n` into a binary representation and repeatedly squaring the base while conditionally multiplying into the result exactly computes `x^n`, and reducing modulo `f(x)` after every multiplication keeps all intermediate polynomials bounded to degree `< k`, giving O(log n) multiplication+reduction rounds, each costing O(k²) (or O(k log k) with FFT). ∎

### Variants / Use Cases
- **Bostan-Mori Algorithm** → faster O(k log k log n) alternative avoiding full polynomial multiplication per step, using generating function relations instead
- **Matrix Exponentiation** → alternative O(k³ log n) method representing the same recurrence as a matrix power, simpler to implement but slower for large `k`
- **Berlekamp-Massey + Kitamasa combo** → a common competitive programming pattern: derive an unknown recurrence from computed terms via Berlekamp-Massey, then jump to a huge index via Kitamasa
- **Linear recurrence-based counting problems (e.g., tiling counts, Fibonacci-like sequences at huge n)** → classic application
- **Cryptographic LFSR state prediction at a future time step** → predicting far-future LFSR outputs efficiently
