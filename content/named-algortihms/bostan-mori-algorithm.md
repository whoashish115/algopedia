---
title: Bostan-Mori Algorithm
---

**Purpose:** Compute the n-th term of a linear recurrence (equivalently, a specific coefficient of a rational generating function `P(x)/Q(x)`), in O(k log k log n) time — faster than Kitamasa's O(k² log n).

### Algorithm
1. Represent the sequence via its generating function `F(x) = P(x) / Q(x)`, where `Q(x)` encodes the linear recurrence (denominator degree `k`) and `P(x)` encodes the initial conditions (numerator degree `< k`).
2. To extract the coefficient of `x^n` in `F(x)`, use the identity `[x^n] P(x)/Q(x) = [x^n] P(x)·Q(-x) / (Q(x)·Q(-x))`. Since `Q(x)·Q(-x)` is even (a polynomial in `x²`), this lets you halve the problem.
3. Compute `P(x)·Q(-x) mod x^(something)`, keep only even-indexed (or odd-indexed, depending on parity of `n`) coefficients, and substitute `x² → x` to get a new, smaller problem of finding `[x^(n/2)]` in a transformed rational function with half-degree denominator.
4. Recurse: this halves `n` each round while keeping the polynomial degree bounded by `k`.
5. After O(log n) rounds, `n` reaches 0, and the answer is directly the constant coefficient of the current numerator divided by the constant term of the current denominator.

### Code
```cpp
typedef vector<long long> Poly;
const long long MOD = 998244353;

Poly polyMul(Poly a, Poly b) { /* use NTT/FFT-based multiplication mod MOD */ 
    Poly res(a.size() + b.size() - 1, 0);
    for (size_t i = 0; i < a.size(); i++)
        for (size_t j = 0; j < b.size(); j++)
            res[i + j] = (res[i + j] + a[i] * b[j]) % MOD;
    return res;
}

long long bostanMori(Poly P, Poly Q, long long n) {
    int k = Q.size() - 1;
    P.resize(k, 0);

    while (n > 0) {
        Poly Qneg = Q;
        for (size_t i = 1; i < Qneg.size(); i += 2) Qneg[i] = (MOD - Qneg[i]) % MOD;

        Poly numer = polyMul(P, Qneg);
        Poly denom = polyMul(Q, Qneg);

        Poly newP(k, 0), newQ(k + 1, 0);
        int parity = n & 1;
        for (int i = parity, j = 0; i < (int)numer.size() && j < k; i += 2, j++) newP[j] = numer[i];
        for (int i = 0, j = 0; i < (int)denom.size() && j <= k; i += 2, j++) newQ[j] = denom[i];

        P = newP; Q = newQ;
        n >>= 1;
    }
    return P[0] * powmod(Q[0], MOD - 2, MOD) % MOD;
}
```
*(`powmod` defined as standard fast modular exponentiation.)*

### Paradigm
**Divide and Conquer.** Each round transforms the problem of extracting coefficient `n` into an equivalent problem extracting coefficient `n/2` from a related rational function of the same denominator degree, halving `n` each time — a textbook divide-and-conquer reduction over the exponent.

### Complexity
- Time: O(k log k log n) using FFT/NTT-based polynomial multiplication
- Space: O(k)

### Proof of Correctness
**Why `[x^n] P(x)/Q(x) = [x^n] P(x)Q(-x) / (Q(x)Q(-x))` holds:** Multiplying numerator and denominator by `Q(-x)` doesn't change the value of the rational function, since `P(x)Q(-x) / (Q(x)Q(-x)) = P(x)/Q(x)` algebraically (as long as `Q(-x) ≠ 0` as a formal power series operation, which holds since we're working formally, not requiring convergence).

**Why `Q(x)·Q(-x)` is even (a function of `x²` only):** For any polynomial `Q(x) = Σ qi xi`, the product `Q(x)Q(-x) = Σ_i Σ_j qi qj xi (-x)j = Σ_i Σ_j qi qj (-1)^j x^(i+j)`. Pairing terms `(i,j)` and `(j,i)`, the odd-total-degree contributions cancel in pairs (swapping `i,j` flips the sign of exactly one factor when `i+j` is odd, since exactly one of `i,j` is odd), leaving only even-degree terms surviving — so `Q(x)Q(-x)` is indeed a polynomial purely in `x²`.

**Why extracting even/odd coefficients and substituting `x² → x` correctly halves the exponent:** Since the denominator `Q(x)Q(-x)` only has even-degree terms, write it as `D(x²)` for some polynomial `D`. The numerator `P(x)Q(-x)` splits into its even part `E(x²)` and odd part `x·O(x²)`. Then `[x^n]` of `(E(x²) + x·O(x²)) / D(x²)` depends only on whether `n` is even or odd: if `n` is even, `[x^n] = [y^(n/2)] E(y)/D(y)`; if odd, `[x^n] = [y^((n-1)/2)] O(y)/D(y)`. Either way, this reduces the problem to extracting coefficient `⌊n/2⌋` from a new rational function `(numer')/(D(y))` — with denominator degree still `k` (same as before, since `D(y)` has the same degree as `Q(x)Q(-x)/2` in the new variable), justifying the recursive halving.

**Termination and final answer:** After O(log n) halvings, `n` reaches 0, at which point `[x^0] P(x)/Q(x) = P(0)/Q(0)` directly (the constant term ratio), since the constant term of a power series quotient is simply the ratio of the two constant terms (assuming `Q(0) ≠ 0`, guaranteed by the recurrence's normalization). ∎

### Variants / Use Cases
- **Kitamasa's Algorithm** → equivalent purpose but O(k² log n) via direct polynomial exponentiation instead of the halving-and-folding trick, simpler to implement
- **Finding n-th Fibonacci-like term modulo a prime, extremely fast** → common competitive programming application when `k` is small but `n` is astronomically large
- **Linear recurrence-based generating function extraction in combinatorics** → broadly useful whenever a sequence satisfies a known linear recurrence
- **P-recursive sequence evaluation** → related techniques extend to sequences satisfying polynomial-coefficient recurrences
- **Berlekamp-Massey + Bostan-Mori combo** → derive the recurrence from data, then evaluate a huge term efficiently