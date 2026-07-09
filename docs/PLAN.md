# Plan

**Status:** Living document — update as work lands, in the same commit as the
work where possible. Tasks cite spec/design IDs; if a task has no grounding
ID, either the docs are missing something (fix them first, CC-1) or the task
doesn't belong.

Statuses: `[ ]` todo · `[~]` in progress · `[x]` done · `[!]` blocked.
Milestones are sequential by default; backlog items are unscheduled until
pulled into a milestone.

---

## M0 — Foundations ✅ (completed 2026-07-10)

Concept validated, engine specified, UI golden path proven.

- [x] Concept analysis: brain-as-world-state, prior art, hard problems
- [x] Architecture decisions D-001..D-013 recorded
- [x] `docs/ENGINE-SPEC.md` v0.1.0, `docs/DESIGN.md` v0.1.0
- [x] Golden-path map widget `ui/travel-map.html` (UI-1..6): parchment style,
      fog of war, rumoured POIs, route, hand-drawn terrain, deterministic jitter
- [x] README: project thesis — human-centric digital twin (D-014)
- [x] Terrain art v2: reference-studied ink cartography (double-summit peaks
      with flank hatching, occluding pine masses, hill clusters, marsh tufts,
      wobbled coastlines with water-lining) + `ui/terrain-lab.html` dev harness
- [x] Tile set completion: village + city terrains (cities tile across
      contiguous hexes), 4-kind rotatable/flippable river overlay set (UI-4 v3)
- [x] Git repo initialized with initial commit

## M1 — World brain scaffold

The empty-but-correct vault. Engine structure, no setting content yet.
**Exit:** every WB/EV/KN structural requirement satisfiable by example; a
dummy entity of each kind passes schema review.

- [ ] Create `world/` tree per WB-1
- [ ] Frontmatter templates per entity kind (WB-2, WB-3) in `world/meta/`
- [ ] Event file format + first example events (EV-1..3)
- [ ] `world/state/`: clock.md, player.md, map.md formats (WB-4, TM-1)
- [ ] `world/knowledge/player.md` format (KN-1, KN-5)
- [ ] Subsystem file template with all five SS-1 parts + worked example
- [ ] Character file template incl. tier field + T3 arc block (SIM-1..4, SS-3)
- [ ] Thread template with heat/status (ND-3)

## M2 — Pocket setting: the first region

Hand-authored content at DESIGN §11 altitude. Small enough to inspect by eye.
**Exit:** DESIGN §11 smell tests pass — every area in 2+ footprints (SS-4),
every subsystem manifests ≥3 ways, every anchor character wants something
from another anchor.

- [ ] World identity + style bible in `world/canon/` (PR-2, DESIGN §8)
- [ ] 1 region: hex grid + terrain (reuse Barrowford demo geography or replace)
- [ ] ~5 subsystems, all five parts each, overlapping footprints (SS-1, SS-4)
- [ ] ~10 organisations
- [ ] ~50 characters: handful of anchors at T2 detail, rest as stubs
- [ ] ~8 places/POIs incl. rumoured-only ones (KN-5)
- [ ] Player character + starting knowledge file
- [ ] Initial threads seeded from subsystem tensions (ND-1, ND-3)

## M3 — The game loop

Playable skills over the vault. Single-pass prose is acceptable here; the
crew comes in M5.
**Exit:** a full session loop — scene → travel → beats → arrival → tick —
runs end-to-end with the map widget re-rendering per UI-6.

- [ ] `/scene` skill (RT-2): location play, manifestation sampling (SS-1.5)
- [ ] `/travel` skill: route candidates w/ differing footprint exposure,
      embarkation choices, director-paced beats (DESIGN §4, ND-1)
- [ ] `/tick` skill (TM-2..4): subsystem dynamics, couplings, T3 arcs,
      pending futures (ND-4), event trail
- [ ] Adjudication rules doc + flow (AD-1..5): impact grading, ledger
- [ ] Event write-path wired: every beat outcome → event → view updates (EV-1)
- [ ] Knowledge propagation: event visibility → player.md (KN-2)
- [ ] Map STATE builder: pure render from world/state + knowledge (AR-1, UI-4)

## M4 — Seam validation

The make-or-break tests. Expect iteration on SIM/AD/ND wording as reality
bites (CC-1: amend spec as we learn).
**Exit:** VA-1, VA-2, VA-3, VA-5 pass; VA-6 verdict recorded honestly.

- [ ] VA-1 catch-up seam: leave, tick 3 months, return
- [ ] VA-2 impact seam: minor-vs-major against one subsystem
- [ ] VA-3 arc seam: promote an NPC, leave, return to a bent arc
- [ ] VA-5 consistency soak: 50 turns + continuity sweep (zero KN-2 leaks)
- [ ] VA-6 entropy test: unattended in-world year → threads worth playing?
- [ ] Continuity-sweeper role prompt (vault-reviewer pattern) to run VA-5

## M5 — The prose crew

Language quality as a subsystem of its own (D-008).
**Exit:** VA-4 blind A/B won by the pipeline (or pipeline revised/rejected
with a DECISIONS entry).

- [ ] Role prompts in `.claude/`: director, writer, editor (PR-1, RT-3)
- [ ] Style bible enforcement + location canon write-path (PR-2, PR-4)
- [ ] Voice sheets created at promotion (PR-2, SIM-4)
- [ ] Foveated crew selection: full vs skeleton per beat type (PR-3)
- [ ] VA-4 blind A/B: 20 beats, pipeline vs single-pass

## M6 — Depth pass

Play it for real; grow by play.
**Exit:** several multi-session campaigns logged; promotion/dormancy observed
in the wild; second region reachable by travel.

- [ ] Second region + inter-region subsystems (trade route, migration...)
- [ ] Arc dormancy in practice (SIM-9): resolve at least one T3 arc
- [ ] Rumour economy: news travels between regions via events (KN-5, TM-2)
- [ ] Session-boot polish (RT-1): cold-start quality from files alone
- [ ] Token/latency budget measurements per beat type (informs PR-3 tuning)

## M7 — Preview-window client (ladder rung 2, RT-4)

Only after the game's soul is proven; the UI was always a pure render (AR-1),
so this is a rendering swap.

- [ ] Local web client: map + prose + input in the preview pane
- [ ] File-queue bridge: page action → queue file → watcher wakes engine
- [ ] World-state file watcher → live page updates
- [ ] Port golden-path aesthetic (richer effects now affordable, D-012 cost
      note no longer applies)

## M8 — Agent SDK app (ladder rung 3, RT-4)

Decision gate, not a commitment: only if M6/M7 say it deserves to be a
product. World brain and skills carry over unchanged.

---

## Backlog (unscheduled)

Pull into a milestone before working on; add grounding IDs when pulled.

- Retrieval at scale: index notes per area/subsystem; embeddings when grep
  stops being enough (D-001 consequence)
- Ollama auxiliary roles: embedding retrieval, classification, bulk coarse
  ticks (D-011 — never the DM)
- World versioning: git snapshot per session / per tick; "load a save" =
  checkout (cheap because vault-is-truth)
- `/worldgen` authoring skill: generate tail content with schema + link
  integrity checks (RT-2, DESIGN §11)
- Map ergonomics: pan/zoom for larger regions; multi-region map view (UI-4
  will need a schema bump)
- Journey encounter variety review: manifestation dedupe so beats don't rhyme
  too often (SS-1.5)
- Weather as a proper subsystem coupled to travel costs (SS-1, DESIGN §4)
- Character sheet / journal widget (second golden-path template, UI-1)
- Soundtrack/ambience — preview-client era at earliest (DESIGN §9 artifact
  conceit applies: diegetic or nothing)
- Multiplayer — parked hard (DESIGN §12 anti-goal); revisit only post-M8
- Public spec extraction: "Claude game engine" as a reusable kit for other
  settings (AR-2 payoff)
- **Worldbuilding layer** (pre-public-release; D-014, DESIGN pillar 6):
  authoring experience for players to build their own settings — guided
  worldgen dialogues, schema-simple templates, canon import, integrity
  checks. The digital-twin promise; large enough to become its own milestone
  when pulled
