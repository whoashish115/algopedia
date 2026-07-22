---
title: Fast Fourier Transform (FFT)
---

**Purpose:** Multiply two polynomials (or compute a discrete Fourier transform) in O(N log N) time, instead of O(N²) with naive convolution.

### Algorithm
1. Given a polynomial of degree `< N` (padded to a power of 2), evaluate it at the `N`-th roots of unity using a divide-and-conquer approach: split the polynomial into its even-indexed and odd-indexed coefficients.
2. Recursively compute the FFT of each half (evaluating each half-sized polynomial at the squares of the roots of unity, which are exactly the roots of unity for the half-sized problem).
3. Combine the two half-results using the "butterfly" operation: for each root of unity `ω`, combine the even and odd parts as `even(ω²) + ω · odd(ω²)` and `even(ω²) - ω · odd(ω²)` to get values at `ω` and `-ω` respectively.
4. This gives the polynomial's values at all `N` roots of unity (point-value representation).
5. To multiply two polynomials: FFT both, multiply the resulting point-values together element-wise, then apply the inverse FFT (same algorithm with conjugated roots of unity, scaled by `1/N`) to convert back to coefficient representation.

### Code
```cpp
typedef complex<double> cd;
const double PI = acos(-1);

void fft(vector<cd>& a, bool invert) {
    int n = a.size();
    if (n == 1) return;

    vector<cd> a0(n / 2), a1(n / 2);
    for (int i = 0; i < n / 2; i++) {
        a0[i] = a[2 * i];
        a1[i] = a[2 * i + 1];
    }
    fft(a0, invert);
    fft(a1, invert);

    double ang = 2 * PI / n * (invert ? -1 : 1);
    cd w(1), wn(cos(ang), sin(ang));
    for (int i = 0; i < n / 2; i++) {
        cd t = w * a1[i];
        a[i] = a0[i] + t;
        a[i + n / 2] = a0[i] - t;
        if (invert) { a[i] /= 2; a[i + n / 2] /= 2; }
        w *= wn;
    }
}

vector<long long> multiply(vector<long long>& a, vector<long long>& b) {
    vector<cd> fa(a.begin(), a.end()), fb(b.begin(), b.end());
    int n = 1;
    while (n < (int)(a.size() + b.size())) n <<= 1;
    fa.resize(n); fb.resize(n);

    fft(fa, false); fft(fb, false);
    for (int i = 0; i < n; i++) fa[i] *= fb[i];
    fft(fa, true);

    vector<long long> result(n);
    for (int i = 0; i < n; i++) result[i] = round(fa[i].real());
    return result;
}
```

### Paradigm
**Divide and Conquer.** The polynomial is split into even/odd halves, each recursively transformed, and combined via the butterfly operation — a direct halving recursion analogous to merge sort's structure.

### Complexity
- Time: O(N log N)
- Space: O(N log N) for the recursion (O(N) with an iterative in-place version)

### Proof of Correctness
**Why splitting into even/odd coefficients works:** For a polynomial `A(x) = A_even(x²) + x·A_odd(x²)` (splitting coefficients by parity), evaluating at the `N`-th roots of unity `ω^0, ω^1, ..., ω^(N-1)` requires evaluating `A_even` and `A_odd` at `ω^(2k)` for various `k` — but since `ω` is a primitive `N`-th root of unity, `ω²` is a primitive `(N/2)`-th root of unity, meaning `A_even` and `A_odd` only ever need to be evaluated at the `(N/2)`-th roots of unity, exactly matching a half-sized FFT problem recursively.

**Why the butterfly combination correctly recovers both `A(ω^k)` and `A(ω^(k+N/2))`:** Since `ω^(k+N/2) = -ω^k` (a defining property of roots of unity — the two halves are always negatives of each other), and `A(x) = A_even(x²) + x·A_odd(x²)`:
```
A(ω^k) = A_even((ω^k)²) + ω^k · A_odd((ω^k)²)
A(-ω^k) = A_even((ω^k)²) - ω^k · A_odd((ω^k)²)
```
Since `(ω^k)² = (-ω^k)²` (squaring removes the sign), both expressions reuse the exact same recursively-computed `A_even` and `A_odd` values at `(ω^k)²`, differing only by the sign of the `ω^k · A_odd(...)` term — exactly the butterfly's add/subtract combination.

**Why convolution via pointwise multiplication works:** Evaluating two polynomials at the same `N` points, multiplying pointwise, and interpolating back recovers the coefficient vector of their product, since polynomial multiplication in coefficient form corresponds exactly to pointwise multiplication in point-value form (a degree-`< N` polynomial is uniquely determined by its values at `N` distinct points — a standard fact from polynomial interpolation theory), as long as `N` is chosen large enough to hold the product's degree without aliasing/wraparound.

**Why the inverse FFT recovers coefficients correctly:** Using conjugate (or negated-angle) roots of unity and scaling by `1/N` inverts the transform, which follows directly from the orthogonality of roots of unity (`Σ ω^(jk) = 0` unless `j ≡ 0 mod N`, in which case it equals `N`) — a standard discrete Fourier transform duality property. ∎

### Variants / Use Cases
- **Number Theoretic Transform (NTT)** → FFT analogue over a finite field, avoiding floating-point precision issues for integer/modular convolution
- **Fast Walsh-Hadamard Transform (FWHT)** → analogous divide-and-conquer transform for XOR/AND/OR convolutions instead of standard polynomial convolution
- **Big integer multiplication** → treating digits as polynomial coefficients, FFT-based multiplication achieves near-linear time for huge numbers
- **Signal processing (audio, image compression)** → FFT is foundational to spectral analysis, filtering, and compression (e.g., JPEG's DCT is closely related)
- **String matching with wildcards** → FFT-based convolution can check pattern matches allowing wildcard characters in O(N log N)
