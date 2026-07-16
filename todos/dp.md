
## 6.5 SOS DP - Sum Over Subsets (`O(N * 2^N)`)

**Problem:** given `f[mask]` for every mask of `N` bits, compute
`g[mask] = sum of f[submask] for every submask of mask` - for *all*
masks simultaneously, faster than the naive `O(3^N)` (which is
already good, but SOS gets you to `O(N * 2^N)`, useful when `3^N` is
still too slow, e.g. large N with tight TL).

**Idea:** process one bit position at a time; at each step, "absorb"
the contribution of turning that bit off.

```cpp
int N = 1 << n;
vector<long long> f(N); // input values indexed by mask
vector<long long> g = f; // dp array, in place

for (int i = 0; i < n; i++) {
    for (int mask = 0; mask < N; mask++) {
        if (mask & (1 << i)) {
            g[mask] += g[mask ^ (1 << i)];
        }
    }
}
// g[mask] now equals sum of f[s] over all submasks s of mask
```
This is the bitmask analogue of a prefix sum, done independently
along each of the `n` bit dimensions.
