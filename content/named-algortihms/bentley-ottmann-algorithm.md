---
title: Bentley-Ottmann Algorithm
---

**Purpose:** Find all pairwise intersection points among N line segments in O((N + K) log N) time, where K is the number of intersections, using a sweep line.

### Algorithm
1. Sweep a vertical line left to right across the plane.
2. Maintain an event queue, ordered by x-coordinate, of segment endpoints and detected intersection points.
3. Maintain a status structure ordered by each segment's current y-coordinate at the sweep line's position.
4. On a left-endpoint event, insert the segment into the status structure and check it against its new immediate neighbors for intersections, adding any found to the event queue.
5. On a right-endpoint event, remove the segment from the status structure and check its now-adjacent former neighbors against each other for a new intersection.
6. On an intersection event, report the intersection point, swap the order of the two crossing segments in the status structure (their y-order flips after crossing), and check each against its new far-side neighbor for further intersections.
7. Continue until the event queue is empty.

### Code
```cpp
struct Point { double x, y; };
struct Segment { Point a, b; int id; };

struct Event {
    double x;
    int type; // 0 = left endpoint, 1 = right endpoint, 2 = intersection
    int segId, segId2;
    Point pt;
    bool operator<(const Event& o) const { return x > o.x; }
};

bool intersect(const Segment& s1, const Segment& s2, Point& out);

vector<Point> bentleyOttmann(vector<Segment> segs) {
    priority_queue<Event> events;
    for (auto& s : segs) {
        Segment ordered = (s.a.x < s.b.x) ? s : Segment{s.b, s.a, s.id};
        events.push({ordered.a.x, 0, ordered.id, -1, ordered.a});
        events.push({ordered.b.x, 1, ordered.id, -1, ordered.b});
    }

    set<int> status; // ordered by segment y-position at current sweep x
    vector<Point> result;

    while (!events.empty()) {
        Event e = events.top(); events.pop();
        if (e.type == 0) {
            // insert segId into status, check both new neighbors via intersect()
        } else if (e.type == 1) {
            // remove segId from status, check its former neighbors against each other
        } else {
            result.push_back(e.pt);
            // swap segId, segId2 in status order, check each against its new far neighbor
        }
    }
    return result;
}
```

### Paradigm
**Decrease and Conquer.** The sweep processes one event at a time, always maintaining a correctly ordered status structure over the segments crossing the current sweep position, reducing the full problem to a sequence of local, adjacent-pair checks.

### Complexity
- Time: O((N + K) log N)
- Space: O(N + K)

### Proof of Correctness
**Invariant:** At any sweep position `x`, the status structure lists exactly the segments currently crossing the sweep line, correctly ordered by their true y-coordinate at `x`, since segments are only reordered exactly when the sweep passes a genuine intersection between two adjacent segments.

**Why checking only adjacent pairs suffices:** Two segments can only become vertically adjacent at insertion, deletion, or immediately after crossing another pair. Since any two segments must first become adjacent before they can intersect (an exchange argument on the sorted order), checking newly adjacent pairs after every event is enough to eventually discover every true intersection before the sweep line reaches it.

**Completeness:** Immediately before any true intersection point, its two segments are adjacent in the status structure (their relative order swaps exactly once, at the crossing), so the algorithm is guaranteed to test that pair and detect the intersection as an event.

**No duplicates, correct termination:** Each segment contributes exactly one left and one right event; each true intersection is discovered exactly once, when its two segments first become adjacent, and processed exactly once (repeat detections of the same point are filtered as duplicates). Since the event queue only ever holds the `2N` endpoint events plus at most `K` intersection events, the algorithm processes a finite number of events and terminates having reported every intersection exactly once. ∎

### Variants / Use Cases
- **Map overlay / GIS polygon intersection** → core subroutine for combining vector map layers
- **CAD system geometry engines** → detecting overlaps and crossings in drawings
- **Axis-aligned rectangle intersection** → simplified variant for orthogonal shapes
- **Robust/degenerate-case variants** → handling overlapping segments, shared endpoints, and vertical segments correctly
- **Arrangement construction** → generalization to arrangements of curves, not just straight segments