---
title: Kadane's Algorithm
---

**Purpose:** Find the maximum sum of a contiguous subarray in an array, in O(n) time.
### Algorithm
1. Keep a running sum, `curSum`, starting at 0. Keep `maxSum` to track the best answer, starting at the smallest possible value (or `arr[0]`).
2. Traverse the array left to right.
3. Add the current element to `curSum`.
4. Update `maxSum` if `curSum` is bigger.
5. If `curSum` becomes negative, reset it to 0 (a negative running sum only drags down future sums, so drop it and start fresh).
6. After traversal, `maxSum` is the answer.

### Code
```cpp
int kadane(vector<int>& arr) {
    int curSum = 0;
    int maxSum = arr[0];

    for (int i = 0; i < arr.size(); i++) {
        curSum += arr[i];
        maxSum = max(maxSum, curSum);
        if (curSum < 0) curSum = 0;
    }
    return maxSum;
}
```

### Paradigm
**Dynamic Programming.** `curSum` at index `i` represents the optimal solution to the subproblem "best subarray sum ending at `i`," built from the optimal solution at `i-1` via a simple recurrence — a classic 1D DP, space-optimized from O(n) to O(1).

### Complexity
- Time: O(n)
- Space: O(1)

### Proof of Correctness
**Claim:** Resetting `curSum` to 0 whenever it goes negative never loses the optimal answer.

**Reasoning:** If `curSum` becomes negative after including some prefix, that prefix is actively hurting any subarray sum it's part of. Any future subarray that starts after this point and includes this negative `curSum` would do strictly better by excluding it and starting fresh. So dropping it (`curSum = 0`) never removes the true maximum — it only removes sums that could never be optimal.

Since `maxSum` is updated at every step with the best `curSum` seen so far, and `curSum` always represents the best sum of a subarray ending at the current index (given the reset rule), `maxSum` correctly converges to the maximum subarray sum by the end of traversal. ∎

### Variants / Use Cases
- **Max sum circular subarray** → `max(Kadane(arr), total - minSubarraySum)`
- **Max product subarray** → track both max and min ending here (negatives flip sign)
- **Max sum rectangle in 2D matrix** → fix row range, collapse to 1D column sums, apply Kadane
- **Max subarray with at most/exactly K elements** → sliding window + Kadane-like update
- **Stock buy-sell (max profit, one transaction)** → Kadane on price differences
- **Print the actual subarray** → track start/end indices during the scan
