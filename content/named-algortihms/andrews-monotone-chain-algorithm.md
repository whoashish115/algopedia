---
title: Andrew's Monotone Chain Algorithm
---

**Purpose:** Compute the convex hull of a set of 2D points, in O(N log N) time, by building the upper and lower hulls separately after sorting by coordinates.

### Algorithm
1. Sort all points lexicographically (by x-coordinate, breaking ties by y-coordinate).
2. Build the **lower hull**: process points left to right, maintaining a stack; while the last three points make a clockwise or straight turn, pop the middle one, then push the current point.
3. Build the **upper hull**: process points right to left using the same turn-popping rule.
4. Concatenate the lower and upper hulls (removing the duplicate endpoints shared between them) to get the full convex hull in counter-clockwise order.

### Code
```cpp
struct Point { long long x, y; bool operator<(const Point& o) const { return x < o.x || (x == o.x && y < o.y); } };

long long cross(Point O, Point A, Point B) {
    return (A.x - O.x) * (B.y - O.y) - (A.y - O.y) * (B.x - O.x);
}

vector<Point> monotoneChain(vector<Point> pts) {
    int n = pts.size();
    if (n < 3) return pts;
    sort(pts.begin(), pts.end());

    vector<Point> hull(2 * n);
    int k = 0;

    for (int i = 0; i < n; i++) {
        while (k >= 2 && cross(hull[k-2], hull[k-1], pts[i]) <= 0) k--;
        hull[k++] = pts[i];
    }

    int lowerSize = k + 1;
    for (int i = n - 2; i >= 0; i--) {
        while (k >= lowerSize && cross(hull[k-2], hull[k-1], pts[i]) <= 0) k--;
        hull[k++] = pts[i];
    }

    hull.resize(k - 1);
    return hull;
}
```

### Paradigm
**Greedy (with a Transform-and-Conquer sort step).** After sorting points by x-coordinate (a preprocessing transform), each of the two scanning passes greedily keeps a point only while it maintains a consistent turn direction, permanently discarding points that create a concave turn.

### Complexity
- Time: O(N log N), dominated by the coordinate sort
- Space: O(N)

### Proof of Correctness
**Why building lower and upper hulls separately covers the full hull:** Every point on the convex hull lies either on the "lower" boundary (the chain of points with no point below the segment connecting hull neighbors) or the "upper" boundary, when points are ordered by x-coordinate. The leftmost and rightmost points are shared between both chains, so building each independently and concatenating (minus duplicate shared endpoints) reconstructs the complete hull.

**Why the turn-popping scan correctly builds each chain:** This mirrors the same invariant argument as Graham Scan: processing points in sorted (x-coordinate) order and popping the stack whenever the last three points form a clockwise or collinear turn maintains the invariant that the stack always represents the correct partial lower (or upper) hull of the points processed so far. A popped point is provably not part of the hull chain at this stage, since its presence would create a concave dent — any point causing this can always be shown to lie strictly below (for lower hull) the segment connecting its stack-neighbors, hence redundant.

**Why sorting by x (not polar angle) still works:** Unlike Graham Scan, no pivot or angular sort is needed because scanning left-to-right and right-to-left directly traces the lower and upper boundaries in a well-defined monotonic order — x-coordinate order guarantees each point is processed exactly once per pass in a consistent geometric direction, avoiding the angular-comparison edge cases (e.g., points with equal angles) that Graham Scan must handle explicitly.

**Termination and correctness:** After both passes, the stack holds every hull vertex exactly once (each chain contributes its unique vertices, with the two shared endpoints deduplicated), giving the correct convex hull. ∎

### Variants / Use Cases
- **Graham Scan** → alternative approach using pivot + polar-angle sort instead of x-coordinate sort
- **Numerically robust hull construction** → monotone chain is often preferred in practice since x-coordinate sorting avoids some polar-angle floating-point pitfalls
- **Convex hull trick (CHT) for DP optimization** → builds a related monotone structure (upper/lower envelope of lines) using similar turn-based logic
- **Collision detection / bounding shapes** → same downstream uses as any convex hull algorithm
- **Half-plane intersection preprocessing** → sorted, monotone-chain-style scans appear in more advanced computational geometry algorithms
