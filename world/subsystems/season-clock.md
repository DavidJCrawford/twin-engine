---
type: subsystem
id: ss-season-clock
created: 2026-07-10
updated: 2026-07-10
status: active
archetype: season-clock
scale: world
layer: mundane
footprint: {areas: all}
variables:
  season: {type: level-like, value: Frostfall, note: "Thaw, Greentide, Harvest, Frostfall"}
  severity: {type: level, value: moderate, drift: rising}
ledger: []
couplings: []
---

## Dynamics
- Reads nothing; everything reads it (SS-15 archetype note).
- Drift: severity rises one step per 10 days through Frostfall; peaks
  mid-winter; falls through Thaw.

## Manifestations
- moderate / sensory: hard frosts holding in shadow; geese moving early.
- high / travel: fords icing; passes closing; daylight short.

## Signature
The season is nobody's doing. Old hands read its pace; nobody reads its mind.
