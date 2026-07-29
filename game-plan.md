# Game Plan — "beat the game 7"

Goal: beat the Ender Dragon.

## Current state

- Position: `x≈52.7, y≈62, z≈-62.3` — standing in a lake/river, at a dirt bank (paused while `in_water`).
- Night of day ~2. Health 20, hunger 20. **Inventory: completely empty (0 wood, 0 tools).**
- Nearby: a **dark forest** on the hilltop to the north (`x≈75–82, z≈-40…-58`); **Zombie Horse** heard nearby (~15 m west).

## Landmarks found

- **Ruined portal**: dark obsidian/gold structure across the water, roughly NW of the first oak (`x≈85, z≈15`?) — seen at `x≈96, z≈31` facing west. Revisit for obsidian/gold/flint-and-steel.

## Lessons learned

### Swimming / water
- **You sink between tool calls** (inter-call latency). Never release `+jump` mid-crossing; do the whole crossing in ONE chain, or keep `+jump` held across calls.
- Deep rivers: watch for underwater dirt overhangs that block surfacing — if stuck (position frozen, breath dropping), back out horizontally while jumping.

### Getting wood (the hard part this session)
- **Breaking blocks is not enough — you must MOVE near the drops** to collect them (pickup radius ~1 block; items despawn in 5 min). All session's "broken" blocks yielded nothing because I stayed put.
- **Fancy/large oaks are a nightmare**: trunk hidden behind 2-high leaf walls; standing nose-close breaks aiming geometry (rays pass *over* a feet-level blocker, so pitch 0–45 can hit nothing). Don't fight a big bushy tree — **pick an isolated clean trunk** (birch/spruce/small oak).
- If stuck against a canopy edge, **move a few blocks along the edge and try another side** (the north side at `z≈30` was open when `z≈33` was walled).
