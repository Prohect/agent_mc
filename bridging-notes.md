# Bridging Notes — placement geometry, flat bridge, stair-bridge (vanilla 26.2)

What this is: how block placement actually works with this mod's freeCursor aiming
(edge-lean geometry, which faces are hittable, why placements silently no-op),
and the verified recipes for flat bridges and back-and-forth-traversable stair-bridges.

## Placement geometry (all verified empirically)

- **Sneak-clamp at an edge settles at edge+0.298** ("the lean"; hitbox 0.002-overlap limit). The last ~0.35 of the approach is SLOW (~10t at ~0.036/t) — budget for it, or start within ~1 block of the edge (the loop-continuation position).
- **The side face of the last block is only visible once the eye is past the corner plane.** From on the deck, voxel raycast always hits the top face first (self-intersection no-op) — forward-facing placement is geometrically impossible.
- **Flat-tap window**: from the lean, pitch **81.5** hits the side face for eye offsets `Δ ∈ [0.242, 0.391]` past the face plane — a 0.15-wide window, position-tolerant once the lean is reached.
- **Top-face placements under/behind you self-intersect** (vanilla blocks placements intersecting the player) → block NOT consumed, silent fail. An occupied target cell also no-ops silently — re-testing a filled cell looks exactly like an aim failure (test-contamination trap).
- **Downward top-face placement only works mid-jump** (feet above the new block's top at apex) — never from standing (auto_tower relies on this).
- Placement at pitch 0 on eye-level wall faces works with a single `tap_use`. Right-clicking an existing table/furnace OPENS it instead — aim beside it.

## `+bridge` — flat bridge

yaw = OPPOSITE of travel (face the structure); +sneak; pitch 81.5.
Per block (20t): `+back wait\16 -back tap_use wait\4` (also `bridge_once`).
Stressed: 7-block void crossing + 20+ flat taps inside stair cycles — all clean.

## `stair_once` / `+stair` — ascending stair-bridge (traversable both ways)

yaw = OPPOSITE of travel; +sneak. Per step (~59t, +1z +1y):
`+back wait\26 -back wait\2 setPitch\81.5 tap_use wait\3 tap_use wait\4 +back wait\8 -back setPitch\90 +jump wait\5 tap_use wait\7 -jump`

- Flat tap at the lean (window guaranteed); 2nd tap = miss redundancy, suppressed by the 4t place-cooldown when the 1st lands (free insurance).
- Walk-on 8t onto the flat block; jump + pitch-90 tap at apex = riser; land, repeat.
- Defects self-heal: missed flat → next cycle retries from same spot; missed riser → line just flattens (still traversable).

**Stress results (25-step build):**
- 26t approach: **15/15 clean, exactly 2.0 blocks/step** (cfg default).
- 20t approach: **5/5 clean, exactly 2.0 blocks/step** — proven faster alternative.
- 18t approach + 6t walk-on: **3 extra placements → riser-stacks (defects)**. Stay ≥20t, keep walk-on 8t.
- **Full 25-step traversal: down AND back up, zero falls.** Descend with `+hike` (plain `+forward` stalls on any 1-high irregularity; hike jumps it).

## Test-protocol pitfalls (bridging-specific)

1. **Ground-level bridging tests are INVALID** — the ray catches the pit floor/far wall and fakes success. Test over real voids (≥15 down, ≥30 long).
2. **Clean your test area**: leftover blocks cause "occupied cell" no-ops that look exactly like aim/geometry failures. Fill air + fresh pad before every re-test.
3. **Traversal tests must walk exactly the structure length** — overshooting the far end and calling it a structure defect is a classic self-own.
4. Per-cycle verification for structure work: after each cycle, cheap-check (y, z, block count) against prediction; stop on mismatch instead of compounding.
