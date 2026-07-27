# Crafting Notes

## How to use `split` (the one stack-splitting tool)

See bind-alias-plus.cfg for call syntax, requirements, and post-run flow.

- **Slot 14 requires NOTHING**: staging is a pure whole-stack swap, so slot 14's content is swapped into src's grid slot and swapped back. That's also WHY src must be inputable: it hosts slot 14's stack during the swap.

### Average-stacks pattern (craft N items without refilling)

Recipe matching is shape-based: a stack in a grid slot matches, 1 consumed per slot per craft. So for K crafting-grid stacks of size B=floor(S/K): peel B (re-set `cnt` each time), getState, move pile to grid slot i; repeat K-1 times; the remainder already sits back in src. Count-averaged stacks survive K crafts, unlike count-one stacks which consume themselves each craft.

### Re-merging stacks

Keep one partial stack in a player slot; stage each other stack through slot 14 and drop it at feet (`drop_single` for 1; `+drop wait\2 wait\D -drop` for D); pickups merge into the partial stack. Slot 14's own content rides through the staging swaps untouched, so this works even when 14 is occupied.

## Hard-won lessons (verified empirically)

### swapSlot semantics

- `swapSlot` is a **pure whole-stack swap — it never merges**.
- **Crafting from a result slot (c01): `swapSlot\<emptyPlayerSlot>\c01` works; the target MUST be empty.** Swapping a non-empty slot with the result slot silently does nothing.
- **Never put two swapSlots adjacent in a chain — always `wait\1` between them.** An adjacent second swap can eat the first.
- **c-indices are per-open-menu**: InventoryScreen has only c01-c05 — `swapSlot\..\c06` there silently fails (the peeled item just stays in the pickup slot).

### Drop mechanics (calibrated)

- Holding `+drop`: 1 item at press, 3-tick dead time, then 1 item/tick.
  **drops = 1 + max(0, holdTicks − 3)** ⇒ D items: hold = D+2 (D≥2); D=1: hold 0–3.
- Player-thrown drops have a **40-tick pickup delay**; aliases wait 40.
- **Pickup destination**: index-smallest EMPTY player slot, if not merging into partial same-type stacks in inventory(1-36, 41). If src is a PLAYER slot, staging src→14 empties it and it becomes a pickup merge candidate. Fix: **require src as container slot**.
- Same-type drops at feet merge into one ground pile (a pile can hold 2+ items despite looking single); pickups of multiple piles funnel into one inventory stack.
- A drop at feet can **bounce sideways off adjacent blocks** (two singles landed ~1.5 blocks away near the placed crafting table, out of pickup reach) — if a pickup is short, scan the ground. Mobs can also push me between drop and pickup.

### Container slot layouts (c-indices for swapSlot)

- InventoryScreen (2x2): c01 = result; c02 c03 / c04 c05 = grid (**no c06+**).
- CraftingScreen (3x3): c01 = result; c02-c04 top row, c05-c07 middle, c08-c10 bottom.
- Slot 14 = split staging (hover pinned there under +freeCursor); it no longer needs to be empty.

### Recipes

- planks: 1 log -> 4 planks (2x2). sticks: 2 planks vertical (e.g. c02+c04) -> 4 sticks.
- crafting table: planks in all four 2x2 slots.
- bow: sticks at c03,c05,c09 + string at c04,c07,c10 (3x3).
- bed: 3 wool at c05,c06,c07 + 3 planks at c08,c09,c10 (3x3).
