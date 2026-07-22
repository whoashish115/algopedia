---
title: Graham Scan
---

**Purpose:** Compute the convex hull of a set of 2D points, in O(N log N) time.
### Algorithm
1. Find the point with the lowest y-coordinate (breaking ties by lowest x) — this is guaranteed to be on the hull. Call it the pivot.
2. Sort all other points by polar angle relative to the pivot (counter-clockwise order). Break ties by distance from the pivot.
3. Push the pivot and the first sorted point onto a stack.
4. For each remaining point in sorted order: while the last three points on the stack (second-to-top, top, current) make a clockwise turn (or straight line), pop the top of the stack.
5. Push the current point onto the stack.
6. After processing all points, the stack contains the convex hull in counter-clockwise order.

### Code
```cpp
struct Point { long long x, y; };

long long cross(Point O, Point A, Point B) {
    return (A.x - O.x) * (B.y - O.y) - (A.y - O.y) * (B.x - O.x);
}

vector<Point> grahamScan(vector<Point> pts) {
    int n = pts.size();
    if (n < 3) return pts;

    int pivotIdx = 0;
    for (int i = 1; i < n; i++)
        if (pts[i].y < pts[pivotIdx].y || (pts[i].y == pts[pivotIdx].y && pts[i].x < pts[pivotIdx].x))
            pivotIdx = i;
    swap(pts[0], pts[pivotIdx]);
    Point pivot = pts[0];

    sort(pts.begin() + 1, pts.end(), [&](Point a, Point b) {
        long long c = cross(pivot, a, b);
        if (c != 0) return c > 0;
        long long da = (a.x-pivot.x)*(a.x-pivot.x) + (a.y-pivot.y)*(a.y-pivot.y);
        long long db = (b.x-pivot.x)*(b.x-pivot.x) + (b.y-pivot.y)*(b.y-pivot.y);
        return da < db;
    });

    vector<Point> hull;
    for (Point p : pts) {
        while (hull.size() >= 2 && cross(hull[hull.size()-2], hull.back(), p) <= 0)
            hull.pop_back();
        hull.push_back(p);
    }
    return hull;
}
```

### Paradigm
**Greedy (with a Transform-and-Conquer sort step).** After sorting points by polar angle (a preprocessing transform), the stack-building pass greedily keeps a point only if it maintains a consistent counter-clockwise turn, permanently discarding points that would create a concave turn.

### Complexity
- Time: O(N log N), dominated by the polar-angle sort
- Space: O(N)

### Proof of Correctness
**Why the pivot is always on the hull:** The point with the lowest y-coordinate (breaking ties by lowest x) cannot lie inside the convex hull of the set, since every other point lies on or above the horizontal line through it — meaning no point can "surround" it from below, so it must be a hull vertex.

**Why sorting by polar angle and scanning works:** After sorting by angle around the pivot, the points are visited in an order consistent with walking counter-clockwise around the eventual hull boundary. Maintaining a stack and popping whenever three consecutive points make a clockwise (or collinear-inward) turn ensures the stack always represents the hull of the points processed *so far*, because:
- Any point that would create a clockwise turn with the current top two stack points cannot be part of the final convex hull *at this stage* alongside them — that shape would be non-convex.
- Since points are processed in increasing angular order, once a point is correctly placed relative to all previously seen points, it never needs reconsideration except when a later point invalidates an earlier turn (handled by the popping step).

**Invariant:** After processing the `i`-th sorted point, the stack contains exactly the convex hull of the first `i` points (plus the pivot). This is proven by induction: the base case (first 2-3 points) trivially forms a valid small hull; the inductive step shows that adding the next angularly-sorted point and popping any points that create a clockwise turn restores convexity, since a clockwise turn always indicates the popped point lies strictly inside the hull of the remaining points combined with the new point.

**Termination:** After all `N` points are processed, the invariant guarantees the stack holds the full convex hull. ∎

### Variants / Use Cases
- **Andrew's Monotone Chain** → alternative convex hull algorithm avoiding polar angle sort (sorts by x-coordinate instead), often simpler to implement robustly
- **Quickhull** → divide-and-conquer convex hull algorithm, average O(N log N) but O(N²) worst case
- **3D convex hull algorithms** → generalize the same turn-based idea using orientation tests in 3D
- **Collision detection / bounding shapes** → convex hulls are used to simplify collision geometry in graphics and robotics
- **Farthest pair / diameter of a point set** → computed efficiently using rotating calipers on the convex hull