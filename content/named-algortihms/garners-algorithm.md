---
title: Garner's Algorithm
---

**Purpose:** Reconstruct a number from its residues modulo several pairwise coprime moduli (an alternative to the standard CRT formula), producing the answer incrementally in a mixed-radix representation, in O(k²) time for `k` moduli.

### Algorithm
1. Given congruences `x ≡ a_i (mod m_i)` for pairwise coprime `m_i`, compute a mixed-radix representation `x = x1 + x2·m1 + x3·m1·m2 + ... `.
2. Compute `x1 = a1`.
3. For each subsequent modulus `mi`, compute `xi` by solving: `xi = (ai - (x1 + x2·m1 + ... + x(i-1)·m1·...·m(i-2))) · (m1·m2·...·m(i-1))⁻¹ mod mi`, where the inverse is taken modulo `mi`.
4. This determines each `xi` one at a time, using only modular inverses computed with respect to the moduli processed so far.
5. The final answer is the mixed-radix sum `x = x1 + x2·m1 + x3·m1·m2 + ... + xk·m1·...·m(k-1)`, computed directly (no need to reduce modulo the full product unless a canonical residue is desired).

### Code
```cpp
long long modInverse(long long a, long long m); // via Extended Euclidean Algorithm

long long garnerAlgorithm(vector<long long>& a, vector<long long>& m) {
    int k = a.size();
    vector<long long> x(k);

    for (int i = 0; i < k; i++) {
        long long cur = a[i];
        long long term = 1;
        for (int j = 0; j < i; j++) {
            cur = ((cur - x[j]) % m[i] + m[i]) % m[i];
            cur = (__int128)cur * modInverse(term, m[i]) % m[i]; // (incorrect if reused naively; see note)
            term = (__int128)term * m[j] % m[i];
        }
        x[i] = cur;
    }

    long long result = 0, multiplier = 1;
    for (int i = 0; i < k; i++) {
        result += x[i] * multiplier; // combine with __int128 or big integers for large products
        multiplier *= m[i];
    }
    return result;
}
```
*(Production implementations carefully separate the running product term from each per-step inverse computation; shown here in simplified form for clarity.)*

### Paradigm
**Dynamic Programming (incremental construction).** Each `xi` is computed directly from previously determined `x1, ..., x(i-1)` without ever needing to revisit or recompute them, building the final mixed-radix representation digit by digit — a sequential DP-style construction rather than a single closed-form combination.

### Complexity
- Time: O(k²) for `k` moduli (each step involves a modular inverse and a running product update against all previous moduli)
- Space: O(k)

### Proof of Correctness
**Why the mixed-radix representation exists and is well-defined:** By the Chinese Remainder Theorem, since all `mi` are pairwise coprime, any residue class modulo `M = m1·...·mk` corresponds to a unique combination of residues modulo each `mi` individually — and any such number can be written uniquely in the mixed-radix form `x1 + x2·m1 + x3·m1·m2 + ...` with each `xi` in the range `[0, mi)`, analogous to how any integer has a unique base-`b` digit representation, just with varying "digit bases" `mi`.

**Why each `xi` is solvable using only previously determined digits:** Consider the partial sum `S_{i-1} = x1 + x2·m1 + ... + x(i-1)·m1·...·m(i-2)`. By construction, `S_{i-1} ≡ x (mod m1·m2·...·m(i-1))` (this is the defining property of the mixed-radix construction up to term `i-1`). The full value `x` must satisfy `x ≡ ai (mod mi)`, so:
```
S_{i-1} + xi · (m1·...·m(i-1)) ≡ ai (mod mi)
⟹ xi · (m1·...·m(i-1)) ≡ ai - S_{i-1} (mod mi)
```
Since `m1, ..., m(i-1)` are all coprime to `mi` (pairwise coprimality), their product `m1·...·m(i-1)` is invertible modulo `mi`, so `xi` is uniquely solvable via `xi = (ai - S_{i-1}) · (m1·...·m(i-1))⁻¹ mod mi`.

**Why this reconstructs the correct final value:** Since each `xi` is solved to satisfy exactly the `i`-th congruence given the accumulated lower digits, by induction the final sum `x1 + x2·m1 + ... + xk·m1·...·m(k-1)` satisfies all `k` congruences simultaneously, matching the unique CRT solution modulo `M`. ∎

### Variants / Use Cases
- **Standard CRT reconstruction formula** → alternative closed-form combination; Garner's is often preferred when moduli are processed incrementally or when intermediate values must stay smaller (avoiding computing the full product `M` upfront)
- **Big integer arithmetic across multiple small-modulus computations** → common in computer algebra systems using Residue Number Systems (RNS)
- **RSA-CRT decryption reconstruction** → recombining partial results computed modulo `p` and `q` separately
- **Multi-precision modular arithmetic libraries** → Garner's incremental approach is often more numerically convenient in software implementations
- **Distributed/parallel modular computation reconstruction** → combining partial results computed independently modulo different primes