---
title: Berlekamp-Massey Algorithm
---

**Purpose:** Find the shortest Linear Feedback Shift Register (LFSR) — equivalently, the minimal linear recurrence relation — that generates a given sequence, in O(N²) time for a sequence of length `N`.

### Algorithm
1. Maintain a current candidate recurrence `C(x)` (initially the trivial recurrence `C(x) = 1`, representing "no recurrence yet") and a "previous" recurrence `B(x)` used for corrections.
2. For each position `i` in the sequence, compute the **discrepancy**: the difference between the actual sequence value at `i` and what the current recurrence `C(x)` would predict.
3. If the discrepancy is 0, the current recurrence still explains the sequence so far — move on.
4. If the discrepancy is non-zero, update `C(x)` by combining it with a shifted, scaled copy of `B(x)` to cancel out the discrepancy, potentially increasing the recurrence length if this is the first correction needed at this length.
5. Keep track of the best "previous" state `B(x)` and the last point where the recurrence length changed, to correctly scale future corrections.
6. After processing the entire sequence, `C(x)` (with degree `L`) represents the shortest linear recurrence consistent with the whole sequence.

### Code
```cpp
vector<long long> berlekampMassey(vector<long long>& s, long long mod) {
    vector<long long> C(1, 1), B(1, 1);
    int L = 0, m = 1;
    long long b = 1;

    auto powmod = [&](long long a, long long e) {
        long long r = 1; a %= mod; if (a < 0) a += mod;
        while (e) { if (e & 1) r = r * a % mod; a = a * a % mod; e >>= 1; }
        return r;
    };

    for (int i = 0; i < (int)s.size(); i++) {
        long long delta = s[i] % mod;
        for (int j = 1; j <= L; j++) delta = (delta + C[j] * s[i - j]) % mod;

        if (delta == 0) { m++; continue; }

        vector<long long> T = C;
        long long coef = delta * powmod(b, mod - 2) % mod;

        while ((int)C.size() < (int)B.size() + m) C.push_back(0);
        for (int j = 0; j < (int)B.size(); j++)
            C[j + m] = (C[j + m] - coef * B[j] % mod + mod) % mod;

        if (2 * L <= i) {
            L = i + 1 - L;
            B = T; b = delta; m = 1;
        } else {
            m++;
        }
    }
    return C;
}
```

### Paradigm
**Greedy (with an incremental correction mechanism).** The algorithm processes the sequence left to right, greedily keeping the current shortest recurrence unless a discrepancy forces a correction, and never revisits earlier decisions except to reuse the stored "previous" correction state.

### Complexity
- Time: O(N²)
- Space: O(N)

### Proof of Correctness
**Why the algorithm always finds *some* valid recurrence:** By construction, whenever a discrepancy is found, the update rule explicitly cancels it out by combining the current recurrence with a scaled, shifted version of the previous correcting polynomial — this guarantees the updated `C(x)` correctly predicts the sequence value at the current position while still correctly predicting all previous positions (since the correction term, being shifted by `m` positions, only affects predictions from position `m` onward, which haven't been finalized yet).

**Why the recurrence length only grows when necessary (minimality):** The key invariant is the rule `if 2L ≤ i, update L = i + 1 - L`. This is derived from a linear-algebraic argument: if the current recurrence of length `L` has correctly predicted `2L` or more consecutive values but then fails, it can be shown (via a rank argument on the associated Hankel matrix of the sequence) that no recurrence of length `L` could possibly explain the sequence up to this new point — forcing the length to increase to exactly `i + 1 - L`, which is provably the *minimum* new length needed. This is the core theorem underlying Berlekamp-Massey's optimality, originally proven in the context of BCH code decoding (Massey, 1969), connecting shortest LFSRs to the structure of Hankel matrix ranks.

**Why the final `C(x)` has minimal degree over the whole sequence:** Since every update only increases `L` when the rank argument above proves it's strictly necessary, and never increases it otherwise, the final recurrence length is the minimum possible degree consistent with the entire observed sequence — any shorter recurrence would necessarily fail to predict some position, contradicting the update rule's forced increases. ∎

### Variants / Use Cases
- **Kitamasa's Algorithm** → uses the recurrence found by Berlekamp-Massey to compute a specific term far in the sequence in O(k² log n) time (k = recurrence order)
- **Bostan-Mori Algorithm** → faster O(k log k log n) alternative to Kitamasa's for the same "find the n-th term" problem
- **Error-correcting codes (BCH, Reed-Solomon decoding)** → originally developed to find the error locator polynomial in decoding algorithms
- **Cryptanalysis of stream ciphers (LFSR-based)** → recovering the shortest LFSR that could have generated an observed keystream reveals a cryptographic weakness
- **Guessing linear recurrences from a sequence of computed DP values** → common competitive programming trick to find a closed-form recurrence from computed terms