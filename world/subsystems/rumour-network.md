---
type: subsystem
id: ss-rumour-network
created: 2026-07-10
updated: 2026-07-10
status: active
archetype: rumour-network
scale: world
layer: mundane
footprint: {areas: all}
variables:
  claims:
    - {claim: "wolves took a drove dog west of the ford", truth: true, origin: "[[ev-1211-fro-01-a]]", reach: local}
    - {claim: "Greywatch Keep stands empty since autumn", truth: false, origin: unknown, reach: local}
    - {claim: "the fair at Nen's Rest will be thin this year", truth: mostly, origin: market talk, reach: local}
    - {claim: "Wren drives with Berrin at dawn", truth: true, origin: "[[ev-1211-fro-01-b]]", reach: village}
ledger: []
couplings:
  - {from: "[[ss-season-clock]].severity", to: reach-speed, sense: "-", strength: weak, latency: 1}
---

## Dynamics
- Drift: claims travel at road speed between settlements; village-internal
  public/local events are known by day's end (KN-6).
- Claims distort with distance; mythic events enter rationalized (LR-4).

## Manifestations
- always / rumour: what innkeepers and wool-buyers already know.

## Signature
No single mouth; ask "who told you?" three times and the trail forks.
