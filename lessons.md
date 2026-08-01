# Agent operation lessons

Sorted by severity: how much it blocks progress vs how avoidable it is.

---

## 🔴 Blocking — no progress until solved

### 1. Crosshair placement failures (block/tool placement silently rejected)
- **distance < ~1.5m**: placement cell intersects player hitbox → rejection with zero feedback. Aim >1.5m away, backup before placing.
- **GUI-entity overlap**: crosshair on crafting table / furnace / chest → `+use` opens GUI instead of placing held block. Turn away, place elsewhere, then walk back.
- **water**: blocks can't be placed ON water; place against a solid block face beside water.
- **furnace-on-ground recipe**: pitch 45° at a stone/granite floor 1.8-2.5m ahead, `+use wait\5 -use`. Needs retry if distance drifts <1.5m (character slides forward).
- **crafting-table-on-wall**: use pitch 15-20° aimed at a granite/stone vertical face at 1.3-2m — places at face cell. Floor-pitch 45° also works (same as furnace).
- **verification pattern**: after `+use`, check `held_item` — if it's still the block, placement failed (no error surfaced by mod).

### 2. Position freeze (player cannot move)
| Cause | Diagnostic signals | Fix |
|---|---|---|
| Invisible entity (trader/llama) in 1-wide passage | footsteps @ 0m, mob sounds @ 0-2m, position unchanged across multiple calls | Dig ceiling or floor to escape around (pitch -90/+90 attack) |
| Gravel-collapse pocket | "Block broken" sounds repeating, "Something trips/falls" + "Player hurts", position locked | Mine downward through floor (pitch 90 attack) |
| Water downflow current against wall | "Splashing" @ 0m, "Water flows" repeated, position locked, drowning | Swim perpendicular away from flow source |

**Detection pattern**: if two consecutive `+hop wait\N -hop` calls show the **same (x,z)**, it's a freeze — stop and diagnose, don't just try harder hops.

### 3. applyRecipe substring matching
- Query `"birch planks"` matched `Birch Wood` (consumed 4 logs → 3 useless wood blocks instead of planks).
- **Fix**: always use full item id: `minecraft:birch_planks`, never name substrings.

---

## 🟠 High-cost — solvable but burns many calls

### 4. Inventory management interactions
- **Container slot numbering**: result slot is `c1` (not `c0`). Grid is `c2..c5` (2×2 inventory) or `c2..c10` (3×3 crafting table).
- **swapSlot off-screen behavior**:
  - `swapSlot\A\B` where A,B ≤ 9 or A = cN → **works with no screen open**.
  - `swapSlot\N\N` where N ≥ 10 (inventory rows) → **needs InventoryScreen or container screen open** (inconsistent, sometimes fails silently).
  - **Safest**: always open the relevant container screen before any swapSlot involving inventory-only slots (10-41).
- **Cursor drops**: after `applyRecipe`, result stays on cursor. If next `swapSlot` hits a full slot or fails, items are lost to the cursor. Always do `swapSlot\c1\freeSlot` immediately after applyRecipe.
- **getState only shows hotbar**: full inventory requires `toggleInventory` open. Hotbar slot 6 contains furnace but furnace-close-screen shows slot empty — it snapped to inventory slot 17 during placement (slot 6 was the placed block's item, now used). Track items across both hotbar AND inventory.

### 5. Furnace smelting timing
- Raw iron → ingot: 200 ticks at any tick rate (10 seconds game-time regardless of tps).
- Sticks as fuel: 1 stick = 5s burn = 1 smelt. 7 sticks = 3.5 smelts — watch for partial-fuel waste.
- **Wait verification**: `wait\430` at 8 tps = ~54 seconds real-time. Check c3 after wait; if empty, add another wait\100.

### 6. Water traversal hazards
- **Magma bubble columns**: identified by constant sinking + "Splashes" at distance. **Never fight the column.** Escape: pure `+forward +sprint` horizontally (no jump). Jump makes it worse.
- **Water downflow shafts**: if "Water flows" + "Splashing here" + unable to rise with jump, mine into a side wall and tower.
- **Rule**: avoid entering any water >1 block deep in this world. Build bridges or go around overland.
- **Died 3 times this session** despite knowing the rule — every accidental water entry (lake shore, underground cave) pulled me into columns. The lake west of spawn is particularly deadly; prefer north overland routes.

---

## 🟡 Annoying — wastes time but doesn't block

### 7. Forward-vector direction errors
- Formula: `forward = (-sin(yaw), +cos(yaw))` in (x, z).
- I verified this multiple times yet still walked west (yaw 90) when intending east (yaw 270).
- **Prevention**: before any long directed move (>5m), screenshot + check the target `pos` after a short hop to confirm direction.

### 8. Tool durability blindness
- Durability only appears in state when that tool is **held in hand** during the `getState` call.
- Pickaxe nearly broke (4/131) without notice, with no spare placed.
- **Fix**: periodically `getState` with the tool in hand before extended mining runs. Craft spare pickaxes early (cobble + sticks are abundant, don't hoard them).

### 9. Slow-tick / real-time tension
- Even at 8 tps, each API cycle (screenshot + decision + alias) costs 200-400 game ticks.
- A game day lasts ~12,000 ticks → ~30-60 tool calls per day. "Simple" tasks can consume an entire day.
- **Strategy**: batch horizontal moves in long `+hop wait\60 -hop` chains. Interleave movement with digging/placing in single calls where possible. Screenshots should be taken only when a decision point is reached.
- **Dynamic tick**: travel at 20 tps (fast real-time), switch to 8 tps for combat/precise building, or lower tps for critical moments.

### 10. loop_tower ceiling failures
- Tower places dirt under you while jumping. Under a solid ceiling, blocks can't place → loop runs silently doing nothing.
- **Pre-check**: screenshot look-up before every `+loop_tower` to verify an open sky path.

### 11. Entity-hunting via screenshots is infeasible
- Spent 2 game-days chasing pigs/sheep/cows, never landed a kill.
- Root cause: between screenshot assessment and attack execution, 200+ game ticks pass — animals have wandered. The `target` field in `getState` never showed an entity (crosshair was always on blocks/grass).
- **Solution**: shears bypass the need to kill sheep (right-click with shears = wool, no aiming). For food: cows are big targets with more HP — still hard. Farming wheat or the sugar cane at the lake shore (for paper → no? for breeding? wheat for bread) is more reliable. Or craft a fishing rod.
- **If hunting is required**: use `+loop_attack_sword` (auto-swing every 13 ticks) while walking into the animal. The loop handles timing; you just aim.

---

## 🟢 Solved — patterns that now work

### Animal hunting replacement: shears
- 2 iron ingots → shears → right-click sheep = 1-3 wool. No combat chasing aiming required, aiming required though.
- 3 wool + 3 planks → bed → set spawn + skip nights.

### Dig-stair climbing
- Pattern: `+attack wait\25 -attack setPitch\-45 +attack wait\25 -attack setPitch\0 +hop wait\12 -hop`
- Climbs +1y per cycle through stone/dirt. Reliable, durability-aware.

### Torch placement
- Place at pitch 40° on the ground 2-2.5m ahead. Works on any solid block (grass, stone, dirt — not cinnabar).
- Held item count decreases when placed → verification.

### Furnace & crafting-table placement
- **Furnace**: pitch 45° at stone/granite floor ≥1.8m away, `+use wait\5 -use`. Open with same pitch +use.
- **Crafting table**: pitch 15-20° at a vertical stone face ≥1.3m away, or pitch 45° at floor like furnace.
- Smelt raw iron: swapSlot input(slot) to c1, fuel to c2; wait 430 ticks per ingot; collect from c3.

### sendCommand quoting
- `sendCommand\"tick rate 8"` — backslash-quotes for multi-word commands inside alias chains.
