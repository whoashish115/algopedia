---
title: Boyer-Moore Algorithm
---

**Purpose:** Find all occurrences of a pattern in a text, with sublinear average-case behavior in practice, by scanning the pattern right-to-left and skipping large chunks of text on mismatches.

### Algorithm
1. Preprocess the pattern to build two heuristics: the **bad character rule** (for each character, the rightmost position it occurs in the pattern) and the **good suffix rule** (how far to shift based on a matched suffix that then mismatched).
2. Align the pattern at the start of the text and compare characters from the **end of the pattern backward**.
3. On a full match (all characters matched), record the occurrence and shift the pattern forward using the good suffix rule.
4. On a mismatch at some pattern position, compute the shift suggested by both the bad character rule (align the mismatched text character with its rightmost occurrence in the pattern) and the good suffix rule (shift based on the already-matched suffix), and take the larger of the two shifts.
5. Repeat until the pattern's start position passes the end of the text.

### Code
```cpp
vector<int> badCharTable(string& pattern) {
    vector<int> last(256, -1);
    for (int i = 0; i < (int)pattern.size(); i++) last[(unsigned char)pattern[i]] = i;
    return last;
}

vector<int> boyerMooreSearch(string text, string pattern) {
    int n = text.size(), m = pattern.size();
    if (m == 0 || m > n) return {};

    vector<int> badChar = badCharTable(pattern);
    vector<int> matches;
    int shift = 0;

    while (shift <= n - m) {
        int j = m - 1;
        while (j >= 0 && pattern[j] == text[shift + j]) j--;

        if (j < 0) {
            matches.push_back(shift);
            shift += (shift + m < n) ? m - badChar[(unsigned char)text[shift + m]] : 1;
        } else {
            int bcShift = j - badChar[(unsigned char)text[shift + j]];
            shift += max(1, bcShift);
        }
    }
    return matches;
}
```
*(Shown with the bad-character rule only, for clarity; production implementations combine it with the good-suffix rule for stronger worst-case guarantees.)*

### Paradigm
**Greedy.** At every alignment, the algorithm greedily takes the largest safe shift suggested by its heuristics and never reconsiders a skipped alignment, trusting the precomputed rules to guarantee no match is skipped over.

### Complexity
- Time: O(N/M) best case (sublinear), O(NM) worst case with bad-character rule alone; O(N + M) worst case when combined with the good-suffix rule
- Space: O(M + alphabet size)

### Proof of Correctness
**Why the bad-character shift is always safe:** On a mismatch at pattern position `j` against text character `c = text[shift+j]`, shifting the pattern so that its rightmost occurrence of `c` aligns with this text position is the furthest the pattern can move while still allowing this exact text character to possibly match that pattern position — any smaller shift would place the pattern's rightmost `c` past this text position, immediately guaranteeing another mismatch at the same spot without any new information. If `c` doesn't appear in the pattern at all, the pattern can safely shift entirely past this position (no alignment overlapping it can ever match), justified similarly.

**Why the shift never skips a true occurrence:** Both the bad-character and good-suffix rules are derived to compute the *minimum* necessary shift that doesn't contradict information already gathered from the current (failed) comparison — i.e., every skipped alignment is one that's provably guaranteed to fail given what's already known (the mismatched character's position, or the matched suffix's structure), not skipped speculatively. Taking the maximum of the two rules' suggested shifts is safe because each rule independently guarantees no valid match is skipped up to its own suggested shift; taking the larger simply combines both guarantees for a stronger (but still safe) skip.

**Right-to-left scanning's role:** Comparing from the end of the pattern first means a mismatch is typically discovered early (many texts differ near the end of a candidate window), letting large sections of text be skipped — this is what gives the algorithm its practical sublinear behavior, though correctness itself doesn't depend on scan direction, only efficiency does. ∎

### Variants / Use Cases
- **Boyer-Moore-Horspool** → simplified variant using only a simplified bad-character rule, easier to implement, still fast in practice
- **KMP / Z-Algorithm** → alternative exact-matching algorithms with guaranteed O(N+M) worst case without needing the good-suffix rule's complexity
- **Text editors / grep-like search tools** → Boyer-Moore's practical speed makes it a common choice for real-world substring search
- **DNA/protein sequence search** → sublinear scanning is valuable on very large genomic texts
- **Multiple pattern variants (Boyer-Moore automaton generalizations)** → less common than Aho-Corasick for multi-pattern search, but related ideas exist
