# Lessons

Rules and constraints the DM has explicitly enforced via the `/lesson` command. Each entry includes the rule itself, the reasoning behind it, and guidance on when it applies.

Claude must read and apply all lessons below at the start of every conversation.

---

## Terrain generation: the data decides, the brush strokes guide

**Rule:** When generating terrain from a painted layer (e.g. `mountain_spine` in
`GM_notes/map_notes/json/`), go with what the **data** says and use the DM's brush strokes
only as a **guide**. A stroke encodes *where* a feature is and *what direction it runs* —
nothing else. Line width is not to scale. Stroke shape is not geology.

**Why:** the DM paints rough intent, not measurements, and measured properties of the
strokes are systematically unlike reality. Painted range paths measure 0.003–0.010 rad/km
of curvature; real mountain crests measure 0.021–0.042 — the strokes are 5–10× straighter
than any real range. Treating a stroke as ground truth produces unnatural terrain. Treating
it as a route, and deriving everything else from real DEM statistics, produces real terrain
in the place the DM wanted it.

**When it applies:** any generator that consumes a hand-painted guide layer.

- Reduce a painted region to its **centreline route**; discard its width entirely.
- Derive width, spur reach, relief, sinuosity, spacing and geological character from
  measured DEM statistics — never from the painting.
- When a measured property of the painting falls outside the range of the real data, that
  is a **finding to report**, not a number to force-fit. Match by *rank* rather than
  absolute value when the two distributions don't overlap.
- Never silently drop part of the painting. If the DM painted mountains on a branch, that
  branch gets mountains.

---

## Lesson 1: No em dashes in prose

**Rule:** Never use em dashes (—) in written prose. Use a comma, semicolon, colon, or
parentheses instead, whichever fits the sentence.

**Why:** The DM does not want em dashes in the writing style for this repo.

**How to apply:** Applies to any prose Claude writes or edits in this repo, including
worldbuilding text, GM_notes content, and character/prologue writing.

---
