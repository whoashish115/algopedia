---
title: Manacher's Algorithm
---

**Purpose:** Find the longest palindromic substring (and the length of the palindrome centered at every position) in a string, in O(N) time.

### Algorithm
1. Transform the string by inserting a unique separator character (e.g., `#`) between every character and at both ends, so all palindromes (odd and even length) become odd-length in the transformed string.
2. Maintain a `P[]` array where `P[i]` is the radius of the palindrome centered at `i` in the transformed string, and a window `[L, R]` representing the rightmost palindrome found so far.
3. For each position `i`: if `i` is inside the current window, initialize `P[i]` using the mirrored position `P[2*center - i]`, capped so it doesn't exceed the window boundary.
4. Try to expand the palindrome at `i` further via direct character comparison beyond the window.
5. If this expansion pushes the palindrome's right edge beyond `R`, update the window `[L, R]` (and center) to this new palindrome.
6. After processing all positions, the largest `P[i]` gives the longest palindromic substring (mapped back to original string coordinates).

### Code
```cpp
string longestPalindrome(string s) {
    string t = "#";
    for (char c : s) { t += c; t += '#'; }

    int n = t.size();
    vector<int> p(n, 0);
    int center = 0, right = 0;

    for (int i = 0; i < n; i++) {
        if (i < right) p[i] = min(right - i, p[2 * center - i]);

        while (i - p[i] - 1 >= 0 && i + p[i] + 1 < n && t[i - p[i] - 1] == t[i + p[i] + 1])
            p[i]++;

        if (i + p[i] > right) { center = i; right = i + p[i]; }
    }

    int maxLen = 0, centerIdx = 0;
    for (int i = 0; i < n; i++) {
        if (p[i] > maxLen) { maxLen = p[i]; centerIdx = i; }
    }
    int start = (centerIdx - maxLen) / 2;
    return s.substr(start, maxLen);
}
```

### Paradigm
**Dynamic Programming (with amortized/two-pointer reuse).** `P[i]` reuses previously computed palindrome radii via mirroring within the current rightmost window, avoiding redundant character comparisons — directly analogous to the Z-algorithm's window-reuse technique.

### Complexity
- Time: O(N)
- Space: O(N)

### Proof of Correctness
**Why the transformed string unifies odd/even palindromes:** Inserting separators between every character (and at the ends) ensures every palindrome in the original string — whether odd or even length — corresponds to exactly one odd-length palindrome centered at some position in the transformed string, since the separator characters "absorb" the parity difference. This lets a single uniform algorithm (which only needs to handle odd-length palindromes cleanly) cover both cases.

**Why mirroring inside the window is valid:** Since `t[L..R]` is a palindrome centered at `center`, the substring structure around any mirrored position `i' = 2*center - i` (for `i` inside `[L, R]`) is a mirror image of the structure around `i`. If `P[i']` (already computed) doesn't reach the boundary of the outer palindrome, then the palindrome at `i` must have exactly the same radius `P[i']`, since it's bounded by a known mismatch just inside the outer palindrome's boundary — no further checking is needed. If `P[i']` reaches exactly to the boundary, the algorithm must extend by direct comparison beyond `R`, since nothing is known about characters past that point.

**Why total work is O(N) (amortized):** Exactly as in the Z-algorithm, every successful direct-comparison expansion pushes `right` strictly further right, and `right` only ever increases across the entire algorithm (bounded by `N`), so total successful-expansion work is O(N); each position additionally does O(1) work for the mirrored lookup and the single failed comparison that stops expansion — giving O(N) total.

**Correctness of the final answer:** Since `P[i]` correctly represents the exact palindrome radius centered at every position `i` (proven above), taking the maximum over all `i` and mapping back to original string coordinates correctly identifies the longest palindromic substring. ∎

### Variants / Use Cases
- **Counting all palindromic substrings** → sum `⌈P[i]/2⌉` over all centers in the transformed string
- **Palindromic factorization / minimum cuts into palindromes** → Manacher's output feeds into DP over palindrome boundaries
- **Eertree (palindromic tree)** → alternative data structure for palindrome-heavy problems, supports more complex queries than Manacher's
- **DNA sequence analysis (palindromic motifs)** → biological sequences often studied for palindromic structure (relevant to restriction sites)
- **Longest palindromic subsequence (different problem)** → note: Manacher's solves *substring* (contiguous), not *subsequence*, which requires a different DP