## 7.3 Nim / Game Theory Connection

XOR shows up outside pure bit-twiddling problems too: in combinatorial
game theory, the **Sprague–Grundy theorem** says a position in a sum
of independent games is a loss for the player to move exactly when
the XOR of the Grundy numbers of all sub-games is `0`. Classic Nim:
the first player wins iff the XOR of all pile sizes is nonzero. This
is why XOR properties (Part 3.2) are worth knowing even for problems
that don't look like "bit manipulation" on the surface.

