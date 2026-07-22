---
title: Dutch National Flag Algorithm
---

**Purpose:** Sort an array containing only 3 distinct values (typically 0s, 1s, 2s) in a single pass, in O(n) time, without extra space.
### Algorithm
1. Use three pointers: `low`, `mid`, and `high`. `low` and `mid` start at index 0, `high` starts at the last index.
2. `low` marks the boundary up to which 0s are placed, `high` marks the boundary from which 2s are placed. `mid` is the current element being examined.
3. While `mid <= high`, look at `arr[mid]`:
   - If it's 0, swap `arr[low]` and `arr[mid]`, then move both `low` and `mid` forward.
   - If it's 1, just move `mid` forward (it's already in the right zone).
   - If it's 2, swap `arr[mid]` and `arr[high]`, then move `high` backward — but don't move `mid`, since the swapped-in element still needs to be checked.
4. Stop when `mid` crosses `high`. The array is now partitioned into three zones: 0s, then 1s, then 2s.

### Code
```cpp
void dutchFlagSort(vector<int>& arr) {
    int low = 0, mid = 0, high = arr.size() - 1;

    while (mid <= high) {
        if (arr[mid] == 0) {
            swap(arr[low], arr[mid]);
            low++;
            mid++;
        } else if (arr[mid] == 1) {
            mid++;
        } else {
            swap(arr[mid], arr[high]);
            high--;
        }
    }
}
```

### Paradigm
**Decrease and Conquer (in-place partitioning).** Each step examines one element and shrinks the unprocessed region `[mid, high]` by one, decreasing the problem size incrementally while maintaining a three-way partition invariant — no subproblems are solved independently and recombined, ruling out Divide and Conquer.

### Complexity
- Time: O(n)
- Space: O(1)

### Proof of Correctness
**Invariant:** At any point during the loop, the array is divided into four regions:
- `[0, low)` → all 0s
- `[low, mid)` → all 1s
- `[mid, high]` → unprocessed elements
- `(high, n-1]` → all 2s

**Base case:** Initially `low = mid = 0` and `high = n-1`, so the first three regions are empty and the unprocessed region is the whole array — invariant holds trivially.

**Inductive step:** Assume the invariant holds before processing `arr[mid]`.
- If `arr[mid] = 0`: swapping it into position `low` extends the 0-region by one, and since `arr[low]` was either the start of the 1-region or equal to `arr[mid]`, it now correctly lands in the mid pointer's old spot as a 1 (or an already-placed 0), so both `low` and `mid` advance safely.
- If `arr[mid] = 1`: it's already correctly placed at the boundary of the 1-region, so only `mid` advances, extending the 1-region.
- If `arr[mid] = 2`: swapping it to `high` extends the 2-region by one. The element brought back from `high` is unprocessed, so `mid` must stay to examine it next.

In each case, the invariant is restored with one region growing and the unprocessed region shrinking by exactly one element.

**Termination:** The unprocessed region `[mid, high]` shrinks by at least one element every iteration, so the loop terminates when `mid > high`, leaving all elements correctly partitioned into 0s, 1s, and 2s. ∎

### Variants / Use Cases
- **Generalized k-way partitioning** → extend to more than 3 distinct values using counting sort or multiple pointers
- **Sort colors problem (LeetCode 75)** → direct application of this algorithm
- **Partitioning around a pivot (QuickSort)** → conceptually related 3-way partition (less than, equal to, greater than pivot)
- **Segregating even/odd or positive/negative numbers** → simplified 2-way version using two pointers
- **Wiggle sort / Dutch flag with duplicates** → variations that handle repeated boundary conditions
