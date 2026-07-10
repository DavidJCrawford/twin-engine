---
type: subsystem
id: ss-wolf-pressure-west-hills
created: 2026-07-10
updated: 2026-07-10
status: active
archetype: predator-population
scale: regional
layer: mundane
footprint: {areas: ["[[barrowford-vale]]"], hexes: "west hills and mountains (see footprints index)"}
variables:
  pressure: {type: level, value: high, drift: "toward high in Frostfall", note: "how boldly packs hunt near men"}
  pack_count: {type: stock, value: 4, unit: packs}
  fear_of_man: {type: level, value: low}
ledger:
  - {date: 1 Frostfall 1211, impact: minor, what: "drove crewed (Wren joins Berrin) — no state move (AD-5)"}
couplings:
  - {from: "[[ss-season-clock]].severity", to: pressure, sense: "+", strength: strong, latency: 1}
  - {from: pressure, to: "[[ss-rumour-network]].claims", sense: "+", strength: strong, latency: 1}
---

## Dynamics
- Drift: pressure one step toward moderate in green seasons, toward high in
  Frostfall and winter (severity coupling).
- If pressure ≥ high for 3 ticks: emit livestock-kill events in bordering
  settled hexes (visibility: local).
- If pressure = critical: packs enter village hexes on dark nights.
- Hunting parties (impact ≥ notable) reduce pack_count; pressure follows
  with 1-tick lag; fear_of_man rises.

## Manifestations
- high / sensory: carcasses by the road, eaten clean; ravens; tracks over tracks.
- high / rumour (true): drovers won't take the west road without pay doubled.
- high / price: mutton scarce; a good dog suddenly beyond price.
- high / encounter: a pack shadowing travellers at dusk, bolder against few.
- high / npc: shepherds sleep in the folds; Berrin watches hills like a door.

## Signature
Wolf-work is eaten, not wasted; tracks shy from iron and fire. A kill left
whole is not wolves, whatever the pump says.
