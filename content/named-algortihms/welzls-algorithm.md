---
title: Welzl's Algorithm
---

**Purpose:** Find the smallest circle enclosing a set of N points in the plane, in expected O(N) time, using randomized incremental construction.

### Algorithm
1. Randomly shuffle the input points.
2. Process points one at a time, maintaining the minimum enclosing circle (MEC) of all points processed so far.
3. If the next point already lies inside the current circle, do nothing — it's already enclosed.
4. If it lies outside, it must sit on the boundary of the new MEC: recompute the MEC of everything processed so far with this point fixed as a boundary point.
5. While fixing one point, if a second point is found outside the current circle-with-one-fixed-point, fix it too and recompute the MEC of the earlier prefix with both points fixed on the boundary.
6. With two boundary points fixed, a third violating point fixes the circle completely, since any circle is uniquely determined by at most three points on its boundary.

### Code
```cpp
struct Point { double x, y; };

double dist(Point a, Point b) { return hypot(a.x - b.x, a.y - b.y); }

struct Circle { Point c; double r; };

bool inside(Circle& c, Point& p) { return dist(c.c, p) <= c.r + 1e-10; }

Circle circleFrom(Point a, Point b) {
    Point c = {(a.x + b.x) / 2, (a.y + b.y) / 2};
    return {c, dist(a, b) / 2};
}

Circle circleFrom(Point a, Point b, Point c) {
    double ax = a.x, ay = a.y, bx = b.x, by = b.y, cx = c.x, cy = c.y;
    double d = 2 * (ax * (by - cy) + bx * (cy - ay) + cx * (ay - by));
    double ux = ((ax*ax+ay*ay)*(by-cy) + (bx*bx+by*by)*(cy-ay) + (cx*cx+cy*cy)*(ay-by)) / d;
    double uy = ((ax*ax+ay*ay)*(cx-bx) + (bx*bx+by*by)*(ax-cx) + (cx*cx+cy*cy)*(bx-ax)) / d;
    Point o = {ux, uy};
    return {o, dist(o, a)};
}

Circle welzl(vector<Point> pts) {
    random_shuffle(pts.begin(), pts.end());
    Circle c = {pts[0], 0};
    for (int i = 1; i < (int)pts.size(); i++) {
        if (!inside(c, pts[i])) {
            c = {pts[i], 0};
            for (int j = 0; j < i; j++) {
                if (!inside(c, pts[j])) {
                    c = circleFrom(pts[i], pts[j]);
                    for (int k = 0; k < j; k++)
                        if (!inside(c, pts[k])) c = circleFrom(pts[i], pts[j], pts[k]);
                }
            }
        }
    }
    return c;
}
```

### Paradigm
**Randomized (Incremental).** Correctness of the fixed-point recursion is deterministic, but the O(N) expected running time relies entirely on processing points in a random order, bounding how often the circle needs updating.

### Complexity
- Time: O(N) expected
- Space: O(1) beyond input storage

### Proof of Correctness
**Claim:** If point `p_i` lies outside the MEC of `{p_1, ..., p_{i-1}}`, then `p_i` must lie on the boundary of the MEC of `{p_1, ..., p_i}`.
**Proof:** Suppose not — then the MEC of `{p_1,...,p_i}` still strictly contains `p_i` in its interior. But that same circle also encloses `{p_1,...,p_{i-1}}`, contradicting the minimality of the MEC of `{p_1,...,p_{i-1}}`, unless the two circles are identical — which is impossible since `p_i` lies outside the smaller one by assumption. Hence `p_i` must lie exactly on the boundary of the new MEC.

The identical argument applies recursively one level down: if a previously-processed point violates the circle built with one (or two) points fixed on the boundary, that violating point must itself lie on the boundary of the correct circle. Since a circle is uniquely determined by three boundary points (or fewer, in degenerate cases), fixing three points in sequence always yields the exact, correct MEC.

**Expected linear time (backward analysis):** The final MEC is determined by at most 3 of the N points. For a uniformly random insertion order, the probability that inserting the `i`-th point requires updating the circle is at most `3/i` (the circle changes only if the newly inserted point is among the ≤3 points that define the final circle, which by random-order symmetry has probability ≤ `3/i`). Summing `O(i) · (3/i)` work over `i = 1` to `N` telescopes to `O(N)` expected total work. ∎

### Variants / Use Cases
- **Minimum enclosing ball in higher dimensions** → generalizes to O(N) expected time in fixed dimension `d`, using `d+1` boundary points
- **LP-type problem framework** → Welzl's algorithm is the canonical example of the general randomized LP-type optimization technique
- **Bounding volume computation** → collision detection and graphics culling in games/simulations
- **Facility location** → smallest circle covering all client locations
- **Smallest enclosing rectangle / ellipse** → related but distinct minimum-enclosing-shape problems