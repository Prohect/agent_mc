# Item Stack Management via BindAliasPlus

> Moving items, splitting stacks, and crafting in most Minecraft scene using BindAliasPlus.

---

## 1. Slot Map

### Persistent (survive GUI close, checked during pickup)

| Slot | Name | Pickup behavior |
|:----:|------|:---|
| 1–9 | Hotbar | merge (1st) · empty-slot (1st) |
| 10–36 | Main inventory | merge (2nd) · empty-slot (2nd) |
| 41 | Offhand | merge (3rd) · empty-slot ❌ |
| 37–40 | Armor | ❌ never checked |

> **Pickup priority:**
> 1. Merge into existing matching stack: hotbar → main inv → offhand
> 2. Place in empty slot: hotbar → main inv (offhand never receives new stacks)
>
> Drop an item with zero matching stacks in hotbar/main/offhand → it lands in the first empty hotbar/main_inventory slot.
> With matching stack in offhand but none in hotbar/main → it merges into offhand.

### Transient (empty on GUI close, skipped during pickup)

| Slots | GUI | Writable? |
|-------|-----|:---:|
| c1–c5 | 2×2 inventory grid | c2-c5 ✅ · c1 ❌ (output-only) |
| c1–c10 | 3×3 crafting table | c2–c10 ✅ · c1 ❌ (output-only) |

> Crafting grid and armor slots are never scanned during pickup.

---

## 2. Core Operations

### 2.1 Move entire stack

```
swapSlot\SOURCE\DEST
```

| Source / Dest | Format | Example |
|:---|--------|---------|
| Hotbar | `1`–`9` | `swapSlot\1\10` |
| Inventory | `10`–`36` | `swapSlot\14\3` |
| Offhand | `41` | `swapSlot\41\c3` |
| 2×2 grid | `c1`–`c5` | `swapSlot\c3\14` |
| 3×3 grid | `c1`–`c10` | `swapSlot\12\c05` |

**Warning:** `swapSlot` **swaps** (not one-way move). Destination contents go to source. If dest is empty this is a move; if dest has items they trade places.

### 2.2 Drop 1 item

```
setPitch\90                        ← mandatory: lands at feet
swapSlot\STACK\14                  ← move stack to slot 14
+drop wait\1 -drop                 ← drop exactly 1
swapSlot\14\STACK                  ← return remainder
```

- Without `setPitch\90`: item gets horizontal velocity, flies outside ~1.5 bl pickup radius.
- Wait **40 ticks** after drop for the item to become pickupable.

### 2.3 Split one stack into N stacks of 1

```
0. Move ALL items of target type into a crafting grid slot
   → Persistent slots now have ZERO of that type

1. Drop 1 (see 2.2)
2. wait\40                         ← PickupDelay
3. swapSlot\PICKUP\cTARGET         ← move single item to target grid slot
4. Repeat 1–3 for each slot needed
```

**Why this works:** persistent slots have 0 of the item → pickup creates a fresh stack of 1 in the first empty hotbar/inventory slot (no merge possible). The crafting grid acts as isolation — it's never scanned.

### 2.4 Take recipe output

```
swapSlot\c1\SLOT                   ← 2×2 or 3×3 output
```

Taking output **consumes 1 from each contributing input slot**. Output refills automatically if inputs remain.

### 2.5 Craft a recipe (generic pattern)

```
1. Identify required input slots from container_grid
2. Park all materials in crafting grid (zero-out persistent slots)
3. For each input slot:
   a. Drop 1 of material at pitch=90
   b. wait\40
   c. Move picked-up item to target grid slot via swapSlot
4. Take output from c1 / c01
```

---

## 3. Drop Mechanics

### Velocity formula

```
vx = -sin(yaw) × cos(pitch) × 0.3
vy = -sin(pitch) × 0.3 + 0.1
vz =  cos(yaw) × cos(pitch) × 0.3
```

### By pitch

| Pitch | Lands | In pickup range? |
|:-----:|-------|:---:|
| **90°** (down) | At feet | ✅ always |
| 0° (horizon) | ~1.3 bl ahead | ❌ out of range |
| −90° (up) | Up then falls | ❌ lands elsewhere |

### Pickup timeline

```
T+0:   ItemEntity spawns, PickupDelay=40
T+1–39: Delay counts down
T+40:  Delay→0, pickup executes this tick
```

- Confirmed: probe at `wait\39` misses, `wait\40` catches.
- Item type agnostic (planks, wool, etc. all behave identically).
- Items on ground merge if same type + within ~0.5 bl of each other.

---

## 4. Recipe Auto-Crafting Rules

- Output appears in `c1` (2×2) or `c01` (3×3) the same tick inputs are placed.
- Inputs are **not consumed** until output is taken.
- More specific recipes take priority (4-plank crafting table beats 1-plank button).
- Extra items in unused grid slots do not block recipe matching.

---

## 5. Verified Writable Grid Slots

### 2×2 inventory grid (`InventoryScreen`)

```literal
|c02:o c03:o|     |c01:o|
|c04:o c05:o|            
```

All of c2/c3/c4/c5 accept `swapSlot` writes. c1 is output-only (recipe output).

### 3×3 crafting table (`CraftingScreen`)

```literal
|c02:o c03:o c04:o|     |c01:o|
|c05:o c06:o c07:o|            
|c08:o c09:o c10:o|            
```

All inputs c2–c10 accept writes. c1 is output-only.

---

## 6. Common Pitfalls

| Symptom | Likely cause |
|---------|-------------|
| swapSlot did nothing | Destination was c1 (blocked) or invalid slot index |
| Item not picked up | Pitch not 90°, or wait < 40 ticks |
| Pickup merged into stack | Persistent slots had same item type — move to grid first |
| Grid items disappeared | GUI was closed — grid is transient |
| Chain behaves unexpectedly | `swapSlot` swaps, not moves one-way; check both slots' contents |
| `c0` error in log | Use `c1`–`c5`, not `c0` |

---

## 7. Quick Recipes

### Crafting table (2×2)

```
[P][P]    Fill c2, c3, c4, c5 each with 1 plank
[P][P]    → c1: crafting_table
```

### Bed (3×3)

```
[W][W][W]    Fill c02, c03, c04 with 1 wool each
[P][P][P]    Fill c05, c06, c07 with 1 plank each
[ ][ ][ ]    → c01: <color>_bed
```

### Planks from logs (2×2)

```
Place 1 log in any input (c2/c3/c4/c5)
→ c1: 4 planks (consumes 1 log when taken)
```

---

## 8. Chain Construction Tips

- **One operation per call when debugging**, batch when confident.
- **`wait\45` gives margin** over the 40-tick pickup delay.
- **Movement keys work in GUI screens** — you can walk while inventory is open.
- **`+attack`/`+use` are suppressed in GUI screens** — can't click grid slots.
- **`slot\N` works in GUI screens** — can change hotbar selection without closing.
- **Verify with `getState` after each step** during development.
- **`getLogDiff`** reveals silent errors (invalid slot args, missing aliases).
