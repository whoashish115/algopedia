---
title: Boyer-Moore's Voting Algorithm
---

**Purpose:** Find the majority element (element appearing more than n/2 times) in an array, in O(n) time and O(1) space.
### Algorithm
1. Initialize a `candidate` (empty/undefined) and a `count` = 0.
2. Traverse the array element by element.
3. If `count` is 0, set the current element as the new `candidate`.
4. If the current element equals `candidate`, increment `count`; otherwise, decrement `count`.
5. After traversal, `candidate` holds a potential majority element.
6. (Optional but recommended) Do a second pass to count occurrences of `candidate` and confirm it actually appears more than n/2 times — needed if a majority element isn't guaranteed to exist.

### Code
```cpp
int majorityElement(vector<int>& arr) {
    int candidate = 0, count = 0;

    for (int i = 0; i < arr.size(); i++) {
        if (count == 0) candidate = arr[i];
        count += (arr[i] == candidate) ? 1 : -1;
    }

    // verification pass
    int freq = 0;
    for (int x : arr) if (x == candidate) freq++;
    return (freq > arr.size() / 2) ? candidate : -1;
}
```

### Paradigm
**Greedy.** At every step it makes a locally optimal choice (keep the current candidate or cancel it against a mismatch) without ever reconsidering past decisions, and this greedy cancellation provably never discards the true majority element.

### Complexity
- Time: O(n)
- Space: O(1)

### Proof of Correctness
**Claim:** If a majority element exists, it always survives as the final `candidate`.

**Reasoning:** Think of `count` as a running "vote tally." Every time the current element matches `candidate`, it casts a +1 vote; every mismatch casts a -1 vote (effectively cancelling one occurrence of `candidate` with one occurrence of something else).

Since the majority element occurs more than n/2 times, it outnumbers all other elements combined. So no matter how the cancellations happen, the majority element cannot be fully cancelled out — at least one of its votes must remain uncancelled by the end.

Whenever `count` hits 0, it means all votes so far have perfectly cancelled out in pairs (one majority-candidate vote against one non-candidate vote, repeatedly). Discarding this prefix and picking a new candidate is safe, because within a fully cancelled prefix, the majority element (if it exists) still holds majority in the remaining suffix — cancellation removes equal numbers of the candidate and non-candidate elements, which cannot remove more majority elements than non-majority elements.

Thus, the true majority element (if one exists) always ends up as the final `candidate`. The verification pass is required because if no majority element exists, this process still returns some candidate — it just won't actually hold majority. ∎

### Variants / Use Cases
- **Majority Element II (n/3 elements)** → generalized version using two candidates and two counters
- **Stream majority element** → same algorithm works online since it processes one pass without storing extra data
- **Find element appearing more than n/k times** → generalized k-candidate version with k-1 counters
- **Verifying dominant element in distributed/streaming systems** → used in load balancing and consensus-like counting problems
- **Bit manipulation majority (single number appearing more than others)** → related idea applied via XOR/bit counting when the majority threshold differs
