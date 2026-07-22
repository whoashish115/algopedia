---
title: Number Theoretic Transform (NTT)
---

**Purpose:** Multiply two polynomials with integer/modular coefficients in O(N log N) time, using modular arithmetic instead of complex roots of unity — avoiding FFT's floating-point precision issues.

### Algorithm
1. Choose a prime modulus `p` of the form `p = c · 2^k + 1` (an "NTT-friendly" prime, e.g., `998244353`), which guarantees the existence of primitive `N`-th roots of unity modulo `p` for `N` up to `2^k`.
2. Find a primitive root `g` of `p`, and compute `ω = g^((p-1)/N) mod p` as the primitive `N`-th root of unity to use (playing the same role as `e^(2πi/N)` in standard FFT).
3. Apply the exact same divide-and-conquer butterfly structure as FFT, but replacing complex arithmetic with modular arithmetic modulo `p`, and `ω` with this modular root of unity.
4. Multiply the two transformed sequences pointwise modulo `p`.
5. Apply the inverse NTT (using `ω⁻¹ mod p`, scaled by `N⁻¹ mod p`) to recover the coefficient representation of the product, entirely within modular arithmetic — no rounding or floating-point error involved.

### Code
```cpp
const long long MOD = 998244353, ROOT = 3; // primitive root of MOD

long long powmod(long long a, long long e, long long m) {
    long long r = 1; a %= m;
    while (e) { if (e & 1) r = r * a % m; a = a * a % m; e >>= 1; }
    return r;
}

void ntt(vector<long long>& a, bool invert) {
    int n = a.size();
    for (int i = 1, j = 0; i < n; i++) {
        int bit = n >> 1;
        for (; j & bit; bit >>= 1) j ^= bit;
        j ^= bit;
        if (i < j) swap(a[i], a[j]);
    }

    for (int len = 2; len <= n; len <<= 1) {
        long long w = powmod(ROOT, (MOD - 1) / len, MOD);
        if (invert) w = powmod(w, MOD - 2, MOD);

        for (int i = 0; i < n; i += len) {
            long long wn = 1;
            for (int j = 0; j < len / 2; j++) {
                long long u = a[i + j], v = a[i + j + len / 2] * wn % MOD;
                a[i + j] = (u + v) % MOD;
                a[i + j + len / 2] = (u - v + MOD) % MOD;
                wn = wn * w % MOD;
            }
        }
    }

    if (invert) {
        long long nInv = powmod(n, MOD - 2, MOD);
        for (long long& x : a) x = x * nInv % MOD;
    }
}

vector<long long> multiplyNTT(vector<long long> a, vector<long long> b) {
    int n = 1;
    while (n < (int)(a.size() + b.size())) n <<= 1;
    a.resize(n); b.resize(n);

    ntt(a, false); ntt(b, false);
    for (int i = 0; i < n; i++) a[i] = a[i] * b[i] % MOD;
    ntt(a, true);
    return a;
}
```

### Paradigm
**Divide and Conquer.** Identical structural recursion to FFT (splitting into even/odd halves via a butterfly network), just carried out entirely within modular arithmetic instead of over the complex numbers.

### Complexity
- Time: O(N log N)
- Space: O(N)

### Proof of Correctness
**Why a modular primitive `N`-th root of unity plays the same role as `e^(2πi/N)`:** The FFT's correctness proof relies only on two algebraic properties of the roots of unity used: (1) `ω^N = 1` and `ω^(N/2) = -1` (needed for the butterfly's `A(ω^k)` / `A(-ω^k)` symmetry), and (2) the orthogonality relation `Σ ω^(jk) = 0` unless `j ≡ 0 (mod N)`. A primitive `N`-th root of unity modulo a prime `p` (guaranteed to exist when `N | (p-1)`, by the cyclic structure of `(Z/pZ)*`) satisfies exactly these same properties within modular arithmetic, so every step of the FFT correctness proof (the even/odd split, the butterfly combination, the interpolation/convolution argument) carries over verbatim, with `mod p` arithmetic replacing complex arithmetic throughout.

**Why choosing `p = c·2^k + 1` matters:** This form guarantees `(Z/pZ)*` has order `p - 1 = c · 2^k`, which is divisible by any power of 2 up to `2^k` — ensuring a primitive `N`-th root of unity exists for any `N` that's a power of 2 up to `2^k`, which is exactly what's needed to run the standard power-of-2 FFT/NTT recursion without needing to pad to an inconveniently large or non-power-of-2 size.

**Why NTT avoids FFT's floating-point error:** Since every operation (addition, multiplication, the roots of unity themselves) stays within exact modular integer arithmetic, there's no rounding error accumulation — the final result is exact, whereas FFT with floating-point complex numbers accumulates small numerical errors that must be corrected via rounding at the end (fine for bounded-size integer coefficients, but can fail for very large coefficients or high precision requirements). ∎

### Variants / Use Cases
- **Convolution modulo a specific prime (competitive programming staple)** → the primary use case, computing polynomial products modulo 998244353 or similar NTT-friendly primes
- **Multiple-modulus NTT + CRT reconstruction** → for convolutions requiring results modulo a non-NTT-friendly number, compute modulo several NTT-friendly primes and combine via CRT
- **Fast big-integer multiplication (exact, no floating point)** → NTT-based multiplication is used in some big-integer libraries for guaranteed-exact results
- **Polynomial exponentiation, division, and other operations built on fast multiplication** → NTT-based convolution is a building block for a wide range of polynomial algorithms
- **Cryptographic applications (lattice-based cryptography, homomorphic encryption)** → NTT is heavily used for fast polynomial ring arithmetic in these schemes