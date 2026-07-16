---
tags: [dsa, data-structure, competitive-programming, trees]
aliases: [BIT, Binary Indexed Tree, Fenwick]
---

# Fenwick Tree (Binary Indexed Tree)

> [!abstract] What is it?
> A **Fenwick Tree** (a.k.a. **Binary Indexed Tree / BIT**) is a compact array-based data structure that supports **prefix sums** (or any invertible associative operation) in `O(log n)` time per update/query, using only `O(n)` space and a much smaller constant factor than a [[Segment Tree]].
>
> It exploits the binary representation of indices: each node `i` is responsible for a range of length `i & (-i)` (the *lowbit* — the value of the lowest set bit).

## Core idea

- `bit[i]` stores the sum of a range `(i - lowbit(i), i]`.
- Moving **up** the tree for an *update*: `i += i & (-i)`
- Moving **down** the tree for a *prefix query*: `i -= i & (-i)`
- 1-indexed by convention (0 has no valid lowbit).

```
lowbit(i) = i & (-i)   // isolates the lowest set bit
```

## Complexity

| Operation | Time | Space |
|---|---|---|
| Point update | O(log n) | O(n) |
| Prefix query | O(log n) | — |
| Range query (sum) | O(log n) | — |
| Build (naive, n updates) | O(n log n) | — |
| Build (O(n) trick) | O(n) | — |

## Reference implementation (uploaded: `fenwick_tree.cpp`)

```cpp
struct Fenwick {
    int n;
    vector<long long> bit;

    Fenwick(int n) {
        this->n = n;
        bit.assign(n + 1, 0);
    }

    void update(int idx, long long delta) {
        while (idx <= n) {
            bit[idx] += delta;
            idx += idx & (-idx);
        }
    }

    long long query(int idx) {
        long long sum = 0;
        while (idx > 0) {
            sum += bit[idx];
            idx -= idx & (-idx);
        }
        return sum;
    }

    long long rangeQuery(int l, int r) {
        return query(r) - query(l - 1);
    }

    // O(n) build — pushes each node's value straight to its parent
    static Fenwick build(const vector<long long>& A) {
        int n = (int)A.size() - 1;
        Fenwick f(n);
        for (int i = 1; i <= n; i++) {
            f.bit[i] += A[i];
            int j = i + (i & -i);
            if (j <= n) f.bit[j] += f.bit[i];
        }
        return f;
    }
};
```

### What each piece does
- `update(idx, delta)` — adds `delta` at position `idx`, propagating to all ancestors that "cover" `idx`.
- `query(idx)` — returns prefix sum `A[1..idx]` by walking down through lowbit jumps.
- `rangeQuery(l, r)` — classic `query(r) - query(l-1)` trick (only valid for invertible ops like `+`, `xor`).
- `build(A)` — **O(n)** construction: instead of calling `update` n times (`O(n log n)`), each index pushes its accumulated value forward to `i + lowbit(i)` once. This is the standard linear-time BIT build.

---

## Variations of the Fenwick Tree

### 1. Point Update, Range Query (PURQ) — *the classic, shown above*
- Update a single element, query prefix/range sums.
- Works for any **invertible** group operation: sum, XOR, product-mod-p (with modular inverse). **Does NOT work directly for min/max** (not invertible) — see variation 5.

### 2. Range Update, Point Query (RUPQ)
- Add `delta` to every element in `[l, r]`, query a single point.
- Trick: use a **difference array** over the BIT.
```cpp
void rangeUpdate(int l, int r, long long delta) {
    update(l, delta);
    update(r + 1, -delta);
}
long long pointQuery(int idx) {
    return query(idx); // prefix sum of the diff array = value at idx
}
```

### 3. Range Update, Range Query (RURQ)
- Needs **two BITs** to support range-sum queries after range updates.
- Maintain `B1` and `B2` such that:
  `prefixSum(i) = i * query(B1, i) - query(B2, i)`
```cpp
Fenwick B1(n), B2(n);
void rangeUpdate(int l, int r, long long delta) {
    B1.update(l, delta);        B1.update(r + 1, -delta);
    B2.update(l, delta * (l-1)); B2.update(r + 1, -delta * r);
}
long long prefixSum(int idx) {
    return idx * B1.query(idx) - B2.query(idx);
}
long long rangeSum(int l, int r) {
    return prefixSum(r) - prefixSum(l - 1);
}
```

### 4. 2D Fenwick Tree (BIT of BITs)
- For matrix prefix sums / 2D point updates.
- `update(x, y, delta)` and `query(x, y)` each do a nested double loop over `x & -x` and `y & -y`.
```cpp
void update(int x, int y, long long delta) {
    for (int i = x; i <= n; i += i & -i)
        for (int j = y; j <= m; j += j & -j)
            bit[i][j] += delta;
}
long long query(int x, int y) {
    long long s = 0;
    for (int i = x; i > 0; i -= i & -i)
        for (int j = y; j > 0; j -= j & -j)
            s += bit[i][j];
    return s;
}
```
- Complexity: `O(log n · log m)` per op.

### 5. Fenwick Tree for Min/Max ("prefix min/max BIT")
- Regular BIT can't subtract to get ranges (min/max aren't invertible), but you **can** still maintain **prefix min/max** if updates only ever *decrease*/*increase* values (monotonic updates) — no arbitrary range queries.
```cpp
void updateMin(int idx, long long val) {
    while (idx <= n) {
        bit[idx] = min(bit[idx], val);
        idx += idx & (-idx);
    }
}
long long queryMin(int idx) { // only prefix, from idx down to 1 via a *different* traversal
    long long res = LLONG_MAX;
    while (idx > 0) {
        res = min(res, bit[idx]);
        idx -= idx & (-idx);
    }
    return res;
}
```
> [!warning]
> True arbitrary range-min queries need a [[Segment Tree]] (or Sparse Table for static arrays) — BIT min/max only gives you prefix semantics with restrictions.

### 6. BIT with Binary Search ("Fenwick walk" / find k-th element)
- Finds the smallest index whose prefix sum ≥ `k` in `O(log n)`, without a separate `query` per step — useful for **order statistics**, **k-th smallest with updates**, **counting inversions**.
```cpp
int findKth(long long k) {
    int pos = 0;
    int logn = 1;
    while ((1 << logn) <= n) logn++;
    for (int pw = 1 << logn; pw > 0; pw >>= 1) {
        if (pos + pw <= n && bit[pos + pw] < k) {
            pos += pw;
            k -= bit[pos];
        }
    }
    return pos + 1; // 1-indexed position of k-th element
}
```

### 7. Fenwick Tree over compressed coordinates
- Not a structural variant but a common pairing: when values/queries span a huge range, **coordinate-compress** first, then BIT operates over compressed indices. Common in "count smaller elements after self", inversion counting, etc.

### 8. Persistent Fenwick Tree
- Each update creates a new "version" (via persistent array techniques or a BIT-of-versions) — lets you query historical prefix sums. Rare; usually a persistent [[Segment Tree]] is preferred instead since BIT persistence is awkward.

---

## Fenwick vs Segment Tree — quick comparison

| Feature | Fenwick Tree | [[Segment Tree]] |
|---|---|---|
| Code complexity | Very simple | More boilerplate |
| Memory | `O(n)` | `O(4n)` typical |
| Supports | Invertible ops (sum, xor) natively | Any associative op (min, max, gcd, sum...) |
| Range update + range query | Needs 2 BITs (trick) | Native w/ lazy propagation |
| Non-invertible ops (min/max) | Awkward / limited | Native |
| Constant factor | Smaller, faster in practice | Slightly larger |
| Conceptual model | Bit manipulation over indices | Explicit binary tree over ranges |

## See also
- [[Segment Tree]]
- Coordinate compression
- Order statistics / k-th order statistic
- Inversion counting
