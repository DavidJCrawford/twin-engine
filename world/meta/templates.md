---
type: canon
id: entity-templates
created: 2026-07-10
updated: 2026-07-10
status: active
---

# Frontmatter templates, one per entity kind (WB-2, WB-3)

Common to every kind: `type`, `id` (stable, immutable), `created`,
`updated`, `status`, optional `aliases`, optional `authorship:
authored | edited | generated` (OB-3). Wikilinks in YAML are quoted.
Mythic content adds `layer: mythic` + `tradition: "[[...]]"` (LR-2, LR-3).
Live examples of every kind exist in `world/` — templates show shape,
examples show life.

## area
    type: area · id · created · updated · status
    grid: {cols, rows, terrain: [rows], rivers: [], roads: [], coasts: []}   # UI-4
    subsystems_touching: ["[[...]]"]        # maintained via footprints index

## place
    type: place · id · ... · area: "[[...]]" · hex: "C4"
    glyph: town|keep|mine|ruin|site · known_by_rumour: bool (KN-5)
    location_canon: "[[...]]"               # PR-2, once first-dressed

## character
    type: character · id · ... · tier: T2|T3 (SIM-1; T0/T1 get no files, SIM-2)
    layer: mundane|mythic|both · tradition: (if mythic)
    home: "[[place]]" · relationships: [{to: "[[...]]", nature}]
    voice: "[[canon voice sheet]]" (PR-2, at promotion)
    # T3 only — the personal-subsystem block (SS-3):
    arc: {goals, means, mood, momentum, couplings: [], fingerprint: "why
      the player matters to this arc (SIM-4)"}

## organisation
    type: organisation · id · ... · seat: "[[place]]"
    purpose · strength: level (SS-5 ordinal)

## subsystem
    (full worked format: docs/SUBSYSTEMS.md §2 — frontmatter: archetype,
    scale, status, footprint, variables {typed, SS-5}, ledger [], couplings
    [{from, to, sense, strength, latency}]; body: Dynamics (drift MANDATORY,
    rules, thresholds), Manifestations (channels, audience?, SS-9),
    Signature)

## tradition
    type: tradition · id · ... · rootedness: rooted|migrant (LR-2)
    peoples: who carries it · beings: ["[[...]]"]
    observance: level (belief-economy hook)

## thread
    type: thread · id · ... · heat: cold|warm|hot · deadline: date? (ND-3)
    parties: [] · subsystems: [] · summary

## agreement (WB-7)
    type: agreement · id · ... · parties: ["[[...]]"]
    status: offered|active|fulfilled|broken|lapsed
    terms: [] · consideration: [{what, when, done: bool}]
    deadline: date · stretch: [{condition, reward}]

## event (EV-3)
    type: event · id: ev-<date>-<seq> · when: in-world date · where: ["[[...]]"]
    actors: [] · impacts: [{subsystem: "[[...]]", magnitude: none|minor|
    notable|major|structural}] (AD-4) · visibility: public|local|
    witnessed:[links]|secret
    Body: what happened, plainly. Append-only (EV-2).

## knowledge (KN-1)
    type: knowledge · id: kn-<character> · character: "[[...]]"
    claims: [{claim, source: "[[event/person]]", confidence: known|
    believed|rumour, date}]      # conflicting claims coexist (KN-5)
    mythic: [...]                # same shape; the twinned's true sight (LR-5)

## canon (PR-2)
    type: canon · id · ... · kind: style-bible|location|voice|identity|templates
    Body: prose canon. Location canon appends, never contradicts (EV-4).
