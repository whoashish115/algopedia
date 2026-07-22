---
title: Fast walsh hadamard transform
---

**Purpose:** Compute the Walsh-Hadamard transform of a sequence of size `n = 2^k` in O(n log n) time, primarily used to perform XOR-convolution between two arrays.

### Algorithm
1. Work on an array of size `n` (must be a power of two).
2. Process the array in `log n` levels, doubling the block size each level (starting at 1, up to `n/2`).
3. At each level, for every block of size `2 * len`, pair up element `i` (in the first half of the block) with element `i + len` (in the second half).
4. Replace the pair `(x, y)` with `(x + y, x - y)` — this is the core "butterfly" operation.
5. After all levels are processed, the array holds the transformed values.
6. To invert the transform, run the exact same butterfly process again, then divide every element by `n`.
7. For XOR-convolution of two arrays `a` and `b`: transform both, multiply them pointwise, then apply the inverse transform to get the convolution result.

### Code
```cpp
void fwht(vector<long long>& a, bool invert) {
    int n = a.size();
    for (int len = 1; len < n; len <<= 1) {
        for (int i = 0; i < n; i += len << 1) {
            for (int j = i; j < i + len; j++) {
                long long u = a[j], v = a[j + len];
                a[j] = u + v;
                a[j + len] = u - v;
            }
        }
    }
    if (invert) {
        for (long long& x : a) x /= n;
    }
}
```

### Paradigm
**Divide and Conquer.** It exploits the recursive structure of the Hadamard matrix `H_{2n} = [[H_n, H_n], [H_n, -H_n]]`, splitting the transform of size `n` into two independent transforms of size `n/2` combined via sum/difference, exactly like the FFT's butterfly recursion but over ±1 instead of complex roots of unity.

### Complexity
- Time: O(n log n)
- Space: O(1) beyond input (in-place)

### Proof of Correctness
**Claim:** the iterative butterfly process computes `H_n · a`, where `H_n` is the Hadamard matrix.

**Base case:** for `n = 1`, `H_1 = [1]`, and the array is trivially unchanged — correct by definition.

**Inductive step:** assume the process correctly computes `H_{n/2} · v` for any array `v` of size `n/2`. Splitting an array of size `n` into its first and second halves and running the smaller transform on each independently (implicit in the level-by-level structure) produces `H_{n/2} · a_{first}` and `H_{n/2} · a_{second}`. The butterfly step then combines corresponding entries as `(x + y, x - y)`, which matches exactly the block structure `H_n = [[H_{n/2}, H_{n/2}], [H_{n/2}, -H_{n/2}]]` applied to the concatenated vector. This is verified directly by algebraic expansion of the block matrix-vector product. Since this holds at every level by induction, the final array after all `log n` levels equals `H_n · a`.

**Invertibility:** since `H_n` is symmetric and `H_n · H_n = n · I`, applying the same transform twice and dividing by `n` recovers the original array exactly. ∎

### Variants / Use Cases
- **XOR convolution** → counting subset-XOR sums, solving problems of the form `c[k] = sum over i^j=k of a[i]*b[j]`
- **OR-convolution / AND-convolution (Sum over Subsets, SOS DP)** → analogous transforms using different butterfly rules for different bitwise operations
- **Fast Fourier Transform (FFT)** → the direct analogue over complex roots of unity instead of ±1
- **Boolean function analysis** → Walsh-Hadamard coefficients are exactly the Fourier spectrum of a Boolean function over GF(2)^n
- **Reed-Muller error-correcting codes** → decoding algorithms are built on the Hadamard transform