---
title: Fortune's Algorithm
---

**Purpose:** Construct the Voronoi diagram of N points in the plane in O(N log N) time using a sweep line and a "beach line" of parabolic arcs.

### Algorithm
1. Sweep a horizontal line downward across the plane.
2. Maintain a "beach line": an ordered sequence of parabolic arcs, where each arc is the set of points equidistant from one site and the sweep line.
3. Maintain two event queues ordered by sweep position: site events (the line reaches a new point) and circle events (three consecutive arcs are about to converge to nothing).
4. On a site event, find the arc directly above the new site, split it in two around a new zero-width arc for the site, and check the newly formed arc triples for potential circle events.
5. On a circle event, the middle arc of the converging triple vanishes; its convergence point is a Voronoi vertex. Record the diagram edges meeting there, remove the arc, and check the newly adjacent arcs for fresh circle events.
6. Continue until both queues are empty. The final beach line traces the diagram's unbounded edges, completing the Voronoi diagram.

### Code
```cpp
struct Point { double x, y; };

struct Event {
    double y;
    bool isSite;
    Point site;
    int arcId;
    bool valid = true;
    bool operator<(const Event& o) const { return y < o.y; }
};

struct Arc {
    Point site;
    int circleEventId = -1;
    Arc *prev = nullptr, *next = nullptr;
};

void handleSiteEvent(Event& e, Arc*& beachLine, priority_queue<Event>& events);
void handleCircleEvent(Event& e, Arc*& beachLine, priority_queue<Event>& events);

void fortune(vector<Point>& sites) {
    priority_queue<Event> events;
    for (auto& s : sites) events.push({s.y, true, s, -1});

    Arc* beachLine = nullptr;
    while (!events.empty()) {
        Event e = events.top(); events.pop();
        if (!e.valid) continue;
        if (e.isSite) handleSiteEvent(e, beachLine, events);
        else handleCircleEvent(e, beachLine, events);
    }
}
```

### Paradigm
**Decrease and Conquer.** The diagram is built incrementally as the sweep line advances, processing one site or vertex event at a time while maintaining the beach line as an always-valid partial solution for everything already swept.

### Complexity
- Time: O(N log N)
- Space: O(N)

### Proof of Correctness
**Invariant:** At sweep position `y`, the beach line is exactly the lower envelope of parabolas focused at all already-processed sites with directrix at the sweep line — i.e., every point below the beach line is strictly closer to some processed site than to the sweep line, so its Voronoi cell assignment is already finalized and will never change.

**Correctness of site events:** When the sweep reaches a new site `s`, the arc directly above `s` belongs to the closest already-processed site at that x-coordinate (by the invariant). Splitting that arc and inserting a zero-width arc for `s` correctly extends the invariant to include `s`.

**Correctness of circle events:** A Voronoi vertex is a point equidistant from three (or more) sites with no site closer. This occurs exactly when three consecutive beach-line arcs converge to a single point as the sweep advances — precisely the condition a circle event detects, since the circle through those three sites has its bottommost point exactly on the sweep line at that moment, with no other site inside it (by the beach-line invariant).

**Completeness and termination:** Each of the N sites generates exactly one site event, and each Voronoi vertex corresponds to exactly one valid circle event (the circle through its three defining, mutually adjacent sites, empty of any other site — a standard bound gives at most `2N - 5` such vertices in a planar Voronoi diagram). Since every event either permanently inserts, splits, or removes a bounded piece of beach-line structure, the queues hold O(N) total events and the algorithm terminates having constructed every edge and vertex exactly once. ∎

### Variants / Use Cases
- **Delaunay triangulation** → the dual graph of the Voronoi diagram, obtainable directly as a byproduct of Fortune's sweep
- **Nearest-neighbor search / closest pair of points** → Voronoi diagrams answer nearest-site queries directly
- **Weighted Voronoi diagrams** → additively or multiplicatively weighted variants for uneven site "importance"
- **Higher-order Voronoi diagrams** → generalization to k-th nearest neighbor regions
- **Mesh generation, motion planning, geographic clustering** → common real-world applications of Voronoi structure