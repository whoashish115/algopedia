---
title: Mo's Algorithm
---

**Purpose:** Answer many offline range queries (e.g., "count distinct elements in `[l, r]`") efficiently by reordering queries, achieving O((N + Q)√N) time instead of O(NQ).

### Algorithm
1. Divide the array into blocks of size roughly `√N`.
2. Sort all queries by (block of `l`, then `r` — with alternating direction of `r` per block for a small constant-factor speedup).
3. Maintain a current window `[curL, curR]` and an incrementally updated answer (e.g., a frequency map and running count).
4. Process queries in the sorted order: move `curL` and `curR` one step at a time (adding/removing elements as they enter/leave the window) until the window matches the query's range.
5. Record the current answer for that query.
6. After processing all queries in this order, map answers back to original query order.

### Code
```cpp
struct Query { int l, r, idx; };

int blockSize;
vector<int> arr, freq;
int curAnswer;

void add(int idx) {
    if (freq[arr[idx]]++ == 0) curAnswer++;
}
void remove(int idx) {
    if (--freq[arr[idx]] == 0) curAnswer--;
}

vector<int> mosAlgorithm(vector<int>& a, vector<Query> queries) {
    arr = a;
    int n = arr.size();
    blockSize = max(1, (int)sqrt(n));
    freq.assign(*max_element(arr.begin(), arr.end()) + 1, 0);

    sort(queries.begin(), queries.end(), [](const Query& x, const Query& y) {
        int blockX = x.l / blockSize, blockY = y.l / blockSize;
        if (blockX != blockY) return blockX < blockY;
        return (blockX & 1) ? (x.r > y.r) : (x.r < y.r);
    });

    vector<int> answers(queries.size());
    int curL = 0, curR = -1;
    curAnswer = 0;

    for (auto& q : queries) {
        while (curR < q.r) add(++curR);
        while (curL > q.l) add(--curL);
        while (curR > q.r) remove(curR--);
        while (curL < q.l) remove(curL++);
        answers[q.idx] = curAnswer;
    }
    return answers;
}
```

### Paradigm
**Transform and Conquer (offline reordering).** The core idea is instance simplification: reordering queries by block so that consecutive queries have minimally different ranges transforms many independent range queries into one long sequence of cheap incremental pointer moves.

### Complexity
- Time: O((N + Q)√N)
- Space: O(N)

### Proof of Correctness
**Correctness of incremental answer maintenance:** `add()` and `remove()` correctly maintain `curAnswer` as the true answer for the current window `[curL, curR]`, since every element entering or leaving the window updates the frequency map and adjusts the distinct-count exactly once per transition — this is a direct invariant maintained by construction, not something requiring a separate proof beyond correct implementation of `add`/`remove`.

**Why the total pointer movement is bounded by O((N + Q)√N):** Group queries by block of `l` (there are `√N` blocks). Within a block, `l` moves at most `blockSize = √N` per query (since all queries in a block share roughly the same `l`-block), so total `l` movement across a block is O(Q_block · √N), and across all blocks, O(Q√N) total. For `r`, since queries within a block are sorted by `r` (in alternating direction for slight optimization), `r` moves monotonically within a block, giving at most O(N) movement per block traversal, and O(N√N) total across all `√N` blocks. Summing both contributions gives O((N + Q)√N) total pointer movements, each O(1) amortized (assuming `add`/`remove` are O(1)).

**Why this correctly answers every query:** Since every query's window is reached via a sequence of valid single-element add/remove operations from wherever the pointers previously stood, and the answer is recorded only once `[curL, curR]` exactly matches `[q.l, q.r]`, every recorded answer is the true answer for its query — order of processing doesn't affect correctness, only efficiency. ∎

### Variants / Use Cases
- **Mo's algorithm with updates (point updates)** → adds a third dimension (time) to the sort, handling offline updates alongside range queries
- **Mo's algorithm on trees** → flatten a tree via Euler tour, then apply the same block-based reordering to subtree/path queries
- **Range mode query / range distinct count** → classic problems solved efficiently with Mo's + incremental frequency tracking
- **Persistence-free alternative to segment trees with Merge Sort tree** → useful when queries are offline and updates are rare or absent
- **Parallel binary search combined with Mo's** → for certain optimization-flavored range query problems
