---
title: Segment Tree
---
> [!abstract] What is it?
> A **Segment Tree** is a binary tree built over an array where every node represents the answer (sum, min, max, gcd, …) for a contiguous **segment/range** of the array. It supports **range queries** and **point/range updates** in `O(log n)`, and generalizes to *any associative operation* - unlike a [[Fenwick Tree (BIT)]], which is restricted mostly to invertible ones.

## Core idea

- Root covers `[0, n-1]`. Each internal node splits its range in half: `[l, mid]` and `[mid+1, r]`.
- Leaves correspond to single array elements.
- A node's value = `combine(leftChild, rightChild)` where `combine` is whatever associative op you need (`+`, `min`, `max`, `gcd`, `and`, `or`, ...).
- Typically stored in an array of size `~4n` using 1-indexed heap-style indices (`2*i`, `2*i+1`).

## Complexity

| Operation | Time | Space |
|---|---|---|
| Build | O(n) | O(4n) |
| Point update | O(log n) | - |
| Range query | O(log n) | - |
| Range update (no lazy) | O(n) | - |
| Range update (with lazy propagation) | O(log n) | - |

## Implementation 

```cpp
struct SegmentTree {
    int n;
    vector<ll> segTree;

    void build(const vector<ll>& nums) {
        n = (int)nums.size();
        segTree.assign(4 * n + 5, 0);
        buildTree(0, n - 1, 1, nums);
    }

    void buildTree(int l, int r, int index, const vector<ll>& nums) {
        if (l == r) { segTree[index] = nums[l]; return; }
        int mid = (l + r) / 2;
        buildTree(l, mid, 2 * index, nums);
        buildTree(mid + 1, r, 2 * index + 1, nums);
        segTree[index] = segTree[2 * index] + segTree[2 * index + 1]; // combine
    }

    void update(int pos, ll val) { updateSegTree(0, n - 1, 1, pos, val); }

    void updateSegTree(int l, int r, int index, int pos, ll val) {
        if (l == r) { segTree[index] = val; return; }
        int mid = (l + r) / 2;
        if (pos <= mid) updateSegTree(l, mid, 2 * index, pos, val);
        else updateSegTree(mid + 1, r, 2 * index + 1, pos, val);
        segTree[index] = segTree[2 * index] + segTree[2 * index + 1];
    }

    ll query(int qL, int qR) { return querySegTree(0, n - 1, 1, qL, qR); }

    ll querySegTree(int l, int r, int index, int qL, int qR) {
        if (r < qL || qR < l) return 0;               // no overlap (identity for sum)
        if (qL <= l && r <= qR) return segTree[index]; // fully inside
        int mid = (l + r) / 2;
        return querySegTree(l, mid, 2*index, qL, qR) + querySegTree(mid+1, r, 2*index+1, qL, qR);
    }

    ll get(int pos) { return query(pos, pos); }
};
```

### Notes on this implementation
- `segTree[index] = ...` line is explicitly marked *"change for min / max"* in the source - this is the single line that determines the tree's behavior (sum vs min vs max vs gcd).
- The **no-overlap identity** returned in `querySegTree` must match the operation:
  - Sum $\rightarrow$ return `0`
  - Min $\rightarrow$ return `LLONG_MAX`
  - Max $\rightarrow$ return `LLONG_MIN`
  - GCD $\rightarrow$ return `0` (gcd(0, x) = x)
- 3 cases per recursive call: **no overlap**, **fully inside**, **partial overlap** - this pattern is universal across all segment tree variants below.

---

## Variations of the Segment Tree

### 1. Min / Max Segment Tree
- Swap the combine line and the "no overlap" identity as noted above.
```cpp
segTree[index] = min(segTree[2*index], segTree[2*index+1]);
// no overlap $\rightarrow$ return LLONG_MAX
```
- Often paired with **position tracking** (store index of min, not just value) for "find the index of the minimum in range" queries.

### 2. GCD / LCM / Bitwise (AND/OR/XOR) Segment Tree
- Same skeleton; only `combine()` and the identity element change.
- XOR segment tree is common for "range XOR query" and works because XOR is associative & has identity `0`.

### 3. Lazy Propagation Segment Tree (Range Update + Range Query)
- Adds a `lazy[]` array. When a range update fully covers a node, you **stamp the update** and defer pushing it to children until they're actually visited (`push_down`).
```cpp
vector<ll> lazy;

void pushDown(int index) {
    if (lazy[index] != 0) {
        for (int child : {2*index, 2*index+1}) {
            lazy[child] += lazy[index];
            segTree[child] += lazy[index]; // depends on range size for sum
        }
        lazy[index] = 0;
    }
}

void updateRange(int l, int r, int index, int qL, int qR, ll val) {
    if (qR < l || r < qL) return;
    if (qL <= l && r <= qR) {
        segTree[index] += val * (r - l + 1); // sum variant
        lazy[index] += val;
        return;
    }
    pushDown(index);
    int mid = (l + r) / 2;
    updateRange(l, mid, 2*index, qL, qR, val);
    updateRange(mid+1, r, 2*index+1, qL, qR, val);
    segTree[index] = segTree[2*index] + segTree[2*index+1];
}
```
- This is the go-to structure for problems like "add v to every element in [l,r], then query range sum" in `O(log n)`.

### 4. Iterative (Bottom-Up) Segment Tree
- Non-recursive, array-based, very fast constant factor - but **only supports point update + range query on invertible-friendly, simpler cases** cleanly (sum, min, max without lazy propagation).
```cpp
vector<ll> tree(2 * n);
void build(vector<ll>& a) {
    for (int i = 0; i < n; i++) tree[n + i] = a[i];
    for (int i = n - 1; i > 0; i--) tree[i] = tree[2*i] + tree[2*i+1];
}
void update(int pos, ll val) {
    pos += n; tree[pos] = val;
    for (pos /= 2; pos >= 1; pos /= 2) tree[pos] = tree[2*pos] + tree[2*pos+1];
}
ll query(int l, int r) { // [l, r)
    ll resL = 0, resR = 0;
    for (l += n, r += n; l < r; l /= 2, r /= 2) {
        if (l & 1) resL += tree[l++];
        if (r & 1) resR += tree[--r];
    }
    return resL + resR;
}
```
- Simpler & faster than recursive but lazy propagation is much harder to bolt on.

### 5. Merge Sort Tree
- Each node stores a **sorted vector** of the elements in its range (built via merge during build, like merge sort).
- Enables queries like "count elements ≤ x in range [l, r]" via binary search at each visited node - `O(log^2 n)` per query.

### 6. Persistent Segment Tree
- Every update creates a **new root** while reusing all unchanged subtrees (only `O(log n)` new nodes per update).
- Enables querying **any past version** of the array - used for "k-th smallest in range" (with merge sort tree idea + persistence), historical range sums, etc.

### 7. Segment Tree Beats
- Advanced variant supporting operations like "range chmin" (`a[i] = min(a[i], x)` for a range) combined with range sum queries, amortized `O(log^2 n)`. Tracks max, second-max, and count-of-max per node to know when to stop recursing.

### 8. 2D Segment Tree / Segment Tree of Segment Trees
- Outer segment tree over rows, each node holding an inner segment tree over columns - for 2D range queries with updates. Heavier than a 2D BIT but supports non-invertible ops (min/max in 2D).

### 9. Dynamic / Sparse Segment Tree (a.k.a. "Segment Tree on the fly")
- For huge coordinate ranges (e.g. `1e9`) where you can't allocate `4n` nodes upfront. Nodes are created **on demand** with pointers/indices, keeping memory proportional to the number of updates × log(range).
```cpp
struct Node { ll val = 0; int left = 0, right = 0; };
vector<Node> tree(1); // tree[0] unused/sentinel
```
- Frequently combined with **persistence** for "persistent dynamic segment tree" (common in competitive programming for offline range queries over large value domains).

### 10. Segment Tree with Coordinate Compression
- Like the BIT equivalent - compress large/sparse coordinates to a dense range `[0, m)` before building, when the dynamic variant isn't needed (i.e., all queries are known offline).

---

## Segment Tree vs Fenwick Tree - quick comparison

| Feature | [[Fenwick Tree (BIT)]] | Segment Tree |
|---|---|---|
| Code complexity | Very simple | More boilerplate |
| Memory | `O(n)` | `O(4n)` typical |
| Supports | Mainly invertible ops (sum, xor) | Any associative op (min, max, gcd, sum, and/or) |
| Range update + range query | Needs 2 BITs (trick) | Native via lazy propagation |
| Min/Max | Limited to prefix, monotonic updates | Fully native |
| Complex ops (chmin, k-th, persistence) | Hard / not designed for it | Has dedicated variants (Beats, persistent, merge sort tree) |
| Constant factor | Smaller | Larger, but far more general |

## See also
- [[Fenwick Tree (BIT)]]
- Lazy propagation
- Merge sort tree
- Persistent data structures
- Sparse Table (alternative for static range-min/max)
