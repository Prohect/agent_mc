# Mining Notes — block×tool timing, benchmark method, ore detection (vanilla 26.2)

What this is: how long blocks take to mine with which tool, how those numbers were
measured (and how NOT to measure them), and how to use timing mismatches to find ore.

## Precise first-break ticks (binary-searched, durability = oracle)

| tool | block | first-break t | steady-state |
|---|---|---|---|
| iron pick | stone | **9** | ~7 |
| iron pick | iron ore | **10** | — |
| stone pick | stone | **15** | ~12 |
| stone pick | deepslate / cobbled | **25** | ~20 |
| wooden pick | stone | **24** | ~20 |
| stone shovel | gravel | **10** | ~7.5 |
| stone axe | oak log | **17** | — |

- First break costs ~2-4t more than steady-state (swing startup).
- `mt` for move1/+mine1: first-break + 2-3 slack → stone pick: 17 (stone) / 28 (deepslate); wooden: 26; iron: 12.
- `+digdown`: stone pick ≈ 7-8 blocks / 140t (fall auto-continue, less retarget overhead).
- Wooden pick CAN mine stone/cobble/coal; CANNOT harvest iron ore+.

## Benchmark method

**Binary search protocol**: fresh column, `+attack wait\N -attack`, getState durability;
broke → N down, not → N up. ~6 calls per combo. Durability delta is the ground-truth oracle.

## Benchmark pitfalls (each cost me a measurement)

1. **Reach ≈ 4.5 blocks.** Ray loses the face as it recedes → silent stall (durability flat). Stand 1 block from the face.
2. **Penetration:** through the wall → ray on air → rest of hold dead. Thickness ≥ expected breaks + 1.
3. **`/tick freeze` does NOT stop block damage** — it accrues in REAL time while frozen, applies on step. Frozen-step is INVALID for timing. Client tick counter also keeps running while frozen.
4. Small samples are noisy (retarget dead-time varies); binary search on FIRST break is the precise method.
5. tp targets: check for pits (fell into one mid-benchmark, mined air, took damage).

## Timing mismatches = ore detector

- A `mt`-timed mining phase that fails to break its block tells you the block is a DIFFERENT class (e.g., stone-timing fails → deepslate-class or ore). **The mismatch IS the ore detector.** Check tooltip (`getScreenshot`) — ore/special blocks reveal themselves exactly this way.
- **Retrying the same OP with the same timing will fail again** (block class doesn't change). Turn 90° and continue the tunnel — general/common blocks (stone, dirt, gravel) never gate resource gathering; only rare blocks are slow, and those are the ones worth identifying.
- After a mismatch: screenshot (tooltip identifies the block), bump `var\mt` if it's just a harder class (deepslate), or detour around it.
