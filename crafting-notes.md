# Crafting Notes — bow + bed session (2026-07-28)

## State

- Task DONE: crafted `minecraft:bow` (hotbar slot 1) and `minecraft:pink_bed` (hotbar slot 5).
- Player at ~(1.8, 0, 6.3), overworld, flat grassland. A crafting table is placed a few blocks east of there.
- Inventory leftovers: 4 pink wool (slot 2), 39 string (slot 3), 13 apples (slot 4), 1 cherry planks (slot 6), 1 stick (slot 9), 1 cherry pressure plate (slot 11, accidental craft — no way to uncraft).
- Alias `split` is persisted in bind-alias-plus.cfg (with `var cnt 1` predefined).

## How to use `split` (the one stack-splitting tool)

- CALL: `var\src\c<N> [var\cnt\<D>] split` — peels D items (default 1); `cnt` self-resets to 1 after each run (one-shot override: re-set it before EACH multi-item call).
- src MUST be an INPUTABLE container slot (any grid slot, NOT result c01). Stack in player inv? Swap it into a free grid slot first (1 swapSlot).
- AFTER the run: `getState` → the peeled pile sits in the index-smallest EMPTY player slot → move it manually with swapSlot. **No prediction reasoning, no dsc var** — read, then move.
- **Slot 14 needs NOTHING**: staging is a pure whole-stack swap, so slot 14's content is swapped into src's grid slot and swapped back — verified with a stick parked in 14 (came home untouched). That's also WHY src must be inputable: it hosts slot 14's stack during the swap.
- Other requirements: flat ground (not on edge — the pitch-90 drop must land at feet); a container screen open; +freeCursor; **no partial same-type stacks in player inventory during pickup** (they'd absorb the peeled pile — move them into container slots first).
- Never peel cnt >= stack count (drops everything → no split). A 2-stack only with default cnt=1.

### Average-stacks pattern (craft N items without refilling)

Recipe matching is shape-based: a stack in a grid slot matches, 1 consumed per slot per craft. So for K crafting-grid stacks of size B=floor(S/K): peel B (re-set `var\cnt\B` each time), getState, move pile to grid slot i; repeat K-1 times; the remainder already sits back in src → swap it to the last grid slot. Count-averaged stacks survive K crafts, unlike count-one stacks which consume themselves.

### Re-merging stacks

Keep one partial stack in a player slot; stage each other stack through slot 14 and drop it at feet (`drop_single` for 1; `+drop wait\D+2 -drop` for D); pickups merge into the partial stack. Slot 14's own content rides through the staging swaps untouched, so this works even when 14 is occupied.

## Hard-won lessons (verified empirically)

### swapSlot semantics

- `swapSlot` is a **pure whole-stack swap — it never merges**. Swapping two equal-count piles is an invisible no-op (beware false "success").
- **Crafting from a result slot (c01): `swapSlot\<emptyPlayerSlot>\c01` works; the target MUST be empty.** Swapping a non-empty slot with the result slot silently does nothing.
- **Never put two swapSlots adjacent in a chain — always `wait\1` between them.** An adjacent second swap can eat the first (prime suspect behind the original "lost plank" incident).
- **c-indices are per-open-menu**: InventoryScreen has only c01-c05 — `swapSlot\..\c06` there silently fails (the peeled item just stays in the pickup slot).

### Drop mechanics (calibrated)

- Holding `+drop`: 1 item at press, 3-tick dead time, then 1 item/tick.
  **drops = 1 + max(0, holdTicks − 3)** ⇒ D items: hold = D+2 (D≥2); D=1: hold 0–3. The `split` alias bakes this in (`+drop wait\cnt wait\2 -drop`) so `cnt` is the literal item count.
- Player-thrown drops have a **40-tick pickup delay**; aliases wait 45+.
- **Pickup destination**: index-smallest EMPTY player slot, after merging into partial same-type stacks. If src is a PLAYER slot, staging src→14 empties it and it becomes a pickup candidate (bit me twice: pickup landed in src's slot; the park-return then swapped it into the park c-slot — invisible with equal pile counts). Fix: **require src = container slot**.
- Same-type drops at feet merge into one ground pile (a pile can hold 2+ items despite looking single); pickups of multiple piles funnel into one inventory stack.
- A drop at feet can **bounce sideways off adjacent blocks** (two singles landed ~1.5 blocks away near the placed crafting table, out of pickup reach) — if a pickup is short, scan the ground. Mobs can also push the player between drop and pickup.

### Workflow

- Verify with `getState` between critical stages; never batch blind when resources are tight.
- `log\MARKER` at chain end + poll `getLogDiff` to know when long chains finish.
- When state looks wrong, recount totals — item conservation pinpoints which step ate/moved what.

### Container slot layouts (c-indices for swapSlot)

- InventoryScreen (2x2): c01 = result; c02 c03 / c04 c05 = grid (**no c06+**).
- CraftingScreen (3x3): c01 = result; c02-c04 top row, c05-c07 middle, c08-c10 bottom.
- Slot 14 = split staging (hover pinned there under +freeCursor); it no longer needs to be empty.

### Recipes used

- planks: 1 log -> 4 planks (2x2). sticks: 2 planks vertical (e.g. c02+c05) -> 4 sticks.
- crafting table: planks in all four 2x2 slots.
- bow: sticks at c03,c05,c09 + string at c04,c07,c10 (3x3).
- bed: 3 wool at c05,c06,c07 + 3 planks at c08,c09,c10 (3x3).

## cfg changelog (2026-07-28)

- Evolution: `msplit`/`mdrop` → merged into one **`split`** alias. Final simplifications:
  - self-resetting **`cnt`** var (predefined `var cnt 1`, body resets after use) — default peels 1;
  - `cnt` is the **literal item count** (the +2 drop-calibration offset is baked into the alias via `wait\cnt wait\2`);
  - **`predicted` and `dsc` removed** — read the pickup slot from getState, move the pile manually;
  - **src must be an inputable c-slot** — no park var, trivial pickup behavior;
  - **"slot 14 empty" requirement removed** — staging swaps its content out and back (validated).
- cfg slimmed to general tool docs only; the experience/semantics writeup lives here.
- Removed `split_item_stack_from_src_to_dsc` (adjacent-swap hazard + racy auto_drop at count 0).
