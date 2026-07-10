---
type: subsystem
id: ss-nen-of-the-lake
created: 2026-07-10
updated: 2026-07-10
status: active
archetype: hidden-folk
scale: local
layer: mythic
tradition: "[[the-old-rites]]"
footprint: {areas: ["[[barrowford-vale]]"], hexes: "the lake and its shores (see footprints index)"}
variables:
  appeasement: {type: level, value: low, drift: falling, note: "the rites lapsed when the chapel drowned"}
  hunger: {type: level, value: low, drift: rising}
ledger: []
couplings:
  - {from: "[[ss-season-clock]].severity", to: hunger, sense: "+", strength: weak, latency: 2}
  - {from: hunger, to: "[[ss-rumour-network]].claims", sense: "+", strength: weak, latency: 1, note: "enters rationalized (LR-4)"}
---

## Dynamics
- Drift: appeasement falls while no rites are kept; hunger rises one step
  per season of neglect.
- If hunger ≥ high: a taking each dark of the moon (event, visibility:
  local — rationalized as drowning/cramp/thin ice per LR-4).
- Rites kept at the Drowned Chapel (impact ≥ notable) raise appeasement.

## Manifestations
- low / sensory: fish gone from the shallows; cold off the water that sits wrong.
- low / rumour (rationalized): "the lake takes a swimmer some years; cramp."
- rising / encounter {audience: the sighted}: a wake with nothing above it;
  the sense of being counted from below.
- high / npc: Nen's Rest folk stop letting children to the shore, and
  cannot quite say why.

## Signature
Drowned things surface on the third day, faces calm. Nothing is ever found
eaten. The old folk of Nen's Rest still nod to the water; ask them why and
they will laugh and change the subject.
