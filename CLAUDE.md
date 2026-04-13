# Fika Picker — Claude context

A single-file Jekyll/GitHub Pages app that randomises who hosts Fika (Swedish coffee break) each week. Bees hover in the sky, launch on button press, buzz around, then land on flowers. Whoever lands on the Fika flower wins.

## Architecture

- All logic in `index.html`. People list in `_data/people.yml`.
- N people → N bees → N flowers, always 1:1
- Fika flower is always the centre flower: `fikaIdx = Math.floor(n / 2)`
- `assignFlowers()` randomises which bee lands on which flower
- Winner = bee whose `flowerIdx` matches the Fika flower index

## Exclude feature

Before launching, the operator clicks a bee to hide it (`display:none`, `bee.excluded = true`). Hidden bees are skipped in `assignFlowers()`, `startBuzzMove()`, `stopBuzzMove()`, and `settleBees()`.

`assignFlowers()` guarantees the Fika flower is always included in the assigned subset: shuffles non-Fika indices, takes M-1, adds the Fika index, reshuffles, assigns to active bees. This ensures there is always a winner regardless of how many bees are excluded.

## Key invariants to preserve

- The guaranteed-winner logic in `assignFlowers()`
- Excluded-bee filtering in all movement/settling functions
- 1:1 bee/flower count (both always equal `PEOPLE.length`)
