# Diagnostics — result-only state signatures, mismatch→cause, tool-call hygiene

What this is: reading reliable information straight off a `getState` without needing
predictions, what each prediction mismatch usually means, and the rules for making
tool calls reliable (screens, chains, latency).

## Result-only signatures (no prediction needed)

- **x/z fraction = `x.3000000119…` or `x.6999999880…`** (float32 of 0.3/0.7): collision-locked against a block face (hitbox half-width = 0.3). `.3` → pressed against the +face of block floor(coord)−1 (blocked toward −axis); `.7` → pressed against the −face of block floor(coord)+1 (blocked toward +axis). VERIFIED repeatedly.
- **x/z fraction exactly `.5`**: block center — fell into a 1x1 shaft (or tp'd). No wall contact.
- **y exactly integer**: standing on a solid block top.
- **durability delta d after a mining hold**: exactly d blocks broke. Ground truth, movement-independent.
- **held_item null during a mining chain**: tool broke mid-hold; rest of the hold ran at hand speed / no-op. Swap tools 5-10 durability BEFORE estimated exhaustion.
- **held_item_count unchanged after a placement tap**: placement no-op (self-intersection / occupied cell / ray on air). Verify with a screenshot before blaming the aim.
- **position frozen at the same .3/.7 across calls**: pinned, not progressing — investigate, don't retry blindly.
- **health dropping while standing still**: suffocation/lava/mob — abort, reposition (peaceful regen otherwise climbs).
- **logDiff "Unknown alias: X_iter"**: loop alias defined wrong (nested definition flattened — see hygiene rule 4).
- **"fell from a high place" in logDiff**: you died; respawn is at world spawn. Check keepInventory before assuming your items are gone.

## Prediction-mismatch → cause

| expected | observed | cause |
|---|---|---|
| tunnel advance | pos same, durability down | floor lip / 2-high obstacle ahead (pitch-70 phase too short, or gravel fell in) |
| tunnel advance | pos same, durability flat | ray on air (penetrated pocket), reach exceeded, screen ate chain, or world paused |
| blocks broken (mt) | fewer than mt×cycles | harder block class (deepslate ~1.7x stone) or tool broke (held_item null) |
| tower rise | y flat, block count same | ceiling overhead, or wrong slot selected (pickaxe can't place) |
| tower rise | y flat, block count down | drifted off-column into rock — reposition, re-tower |
| chain runs | everything unchanged, `screen` non-null | screen/pause ate it (never lead chains with `esc`; standalone `closeScreen` first) |
| furnace output | fuel gone, no output | fuel burn < 200t smelt time (stick=100t burns out; use ≥200t fuel per smelt) |
| smelt won't start | fuel untouched | input has no smelt recipe (verified: cinnabar, sulfur — decorative) |
| bridge/stair place | block count same | occupied cell or top-face self-intersection (verify cell empty) — or wrong slot selected |
| stair metrics fine but traversal falls | — | riser-stack gap, OR you walked past the structure's end |
| block count > 2/step on stair | — | approach < 20t or walk-on < 8t → extra placements; tighten up |

## Tool-call hygiene

1. **Never lead a chain with `esc` after a screen-open tool call — the whole chain silently no-ops.** Standalone `closeScreen` first, action chain in the next call. (Bit me 3+ times.)
2. Trailing `wait\N` in a chain is a NOP; use the runAlias `delay` param + getState to observe instead of burning calls on wait chains.
3. getState/runAlias return PRE-execution snapshots; a chain started earlier may still be running — check `tick` + logDiff to tell mid-chain states.
4. **Nested `alias\name;chain;...` definitions only work from the CFG file** — via runAlias/defineAlias the `;` converts to spaces when the OUTER alias is stored, so the nested definition lands flat and `builtinRunAlias` can't find the iter alias ("Unknown alias"). Define loop aliases in the cfg only.
5. Keep time-sensitive steps inside ONE runAlias chain; verify with getState after, not between micro-steps.
6. **Human-latency reaction discipline**: tool-call round-trip is unpredictable; game runs at 20tps. Long holds are safe only when no mob/lava variance matters. Where reaction matters (future non-peaceful combat): `tick rate 1` makes an observe→reason→react cycle cost ~1-2 game ticks (`t1`/`t20` aliases ready, cheat-allowed hosts only).
