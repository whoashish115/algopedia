---
title: Main
---

## Brian Kernighan's Algorithm

Counts the number of set bits by repeatedly removing the lowest set bit. Each iteration clears exactly one `1`, so the number of iterations equals the popcount.

Time Complexity:

$$
O(\operatorname{popcount}(n))
$$

```cpp
int count = 0;
while (n) {
    n &= (n - 1);
    count++;
}
```
## Gosper's Hack

Generates the next larger integer having the same number of set bits in constant time, allowing enumeration of all `r`-element subsets represented as bitmasks.

```cpp
unsigned next_combination(unsigned mask) {
    unsigned c = mask & -mask;
    unsigned r = mask + c;
    return (((mask ^ r) >> 2) / c) | r;
}
```

Usage:

```cpp
unsigned mask = (1u << r) - 1;
unsigned limit = 1u << n;

while (mask < limit) {
    // process mask
    mask = next_combination(mask);
}
```

Time Complexity:

$$
O(1)
$$

per generated combination.

## Held–Karp Algorithm

Held–Karp is the classic dynamic programming algorithm for solving the Traveling Salesman Problem using subset DP.

State:

$$
dp[\text{mask}][i]
$$

stores the minimum cost to visit exactly the cities in `mask` and finish at city `i`.

Transition:

$$
dp[\text{mask}\cup\{j\}][j]
=
\min\left(
dp[\text{mask}\cup\{j\}][j],
dp[\text{mask}][i]+w(i,j)
\right)
$$

```cpp
vector<vector<long long>> dp(1 << n, vector<long long>(n, INF));
dp[1 << start][start] = 0;

for (int mask = 0; mask < (1 << n); mask++) {
    for (int i = 0; i < n; i++) {
        if (!(mask & (1 << i)) || dp[mask][i] == INF) continue;
        for (int j = 0; j < n; j++) {
            if (mask & (1 << j)) continue;

            int nextMask = mask | (1 << j);
            dp[nextMask][j] = min(dp[nextMask][j],
                                  dp[mask][i] + cost[i][j]);
        }
    }
}
```

Complexity:

$$
\text{Time} = O(n^2 2^n), \qquad
\text{Space} = O(n2^n)
$$