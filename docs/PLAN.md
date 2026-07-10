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
      contiguous hexes), 5-kind rotatable/flippable river + road overlay
      sets (incl. source springs / road termini), desert + dense forest
      terrains, per-edge coastline styling (beach/cliff on any land hex,
      any edge subset — covers the 12 one-to-six-edge variants and more)
      (UI-4 v3)
- [x] Subsystem mechanics v1 (SS-5..SS-15, D-015) + archetype library:
      `docs/SUBSYSTEMS.md`, ~80 templates in 12 families, worked example,
      authoring checklist, recommended starter set for M2
- [x] Scene-choices widget (UI-8) + UI-6 render-scope amendment + button
      contrast fix — first UI evolution driven by paper playtest session 01
- [x] Open source kit (D-018): MIT LICENSE, CONTRIBUTING.md,
      docs/TWIN-GUIDE.md (template-repo instantiation + engine-remote
      upgrade path), README build-your-own-world + licensing sections
- [x] Git repo initialized with initial commit

## M1 — World brain scaffold ✅ (completed 2026-07-10)

The correct-and-alive vault: populated directly from paper playtest
session 01's canon (D-020), so every required example is a played thing.
**Exit met:** a valid example of every WB-3 entity kind exists in `world/`.

- [x] `world/` tree per WB-1 (incl. traditions/, agreements/ — WB-7)
- [x] Frontmatter templates for all 11 entity kinds: `world/meta/templates.md`
- [x] Event format + example events with impacts/visibility (EV-1..3, AD-4)
- [x] `world/state/`: clock (with pending futures), player (exact hard
      state), map (WB-4, TM-1, DESIGN §10)
- [x] `world/knowledge/player.md` — conflicting claims side by side (KN-5),
      mythic sight ledger (LR-5)
- [x] Subsystem examples with all five SS-1 parts: two cores (season clock,
      rumour network with truth-flagged claims) + mundane (wolf pressure,
      with AD-5 ledger entry) + mythic (Nen, with audience-gated
      manifestations and LR-4 rationalization)
- [x] `world/state/footprints.md` hex→subsystem index (SS-13)
- [x] Character examples: player (gift origin), T2 (Berrin, voice); T3 arc
      block documented in template (SIM-1..4, SS-3)
- [x] Thread with heat + deadline (ND-3 as amended)
- [x] Tradition with rootedness/observance + layer/tradition frontmatter
      (LR-2, LR-3); agreement with staged consideration + stretch (WB-7)

## M2 — Pocket fixture world: the first region

Hand-authored content at DESIGN §11 altitude, small enough to inspect by
eye. Per AR-4 this is an engine dev fixture (generic placeholder, Barrowford
lineage), not a shipped setting — real settings are separate twin
repositories (first planned twin: NZ-1827, D-016).
**Exit:** DESIGN §11 smell tests pass — every area in 2+ footprints (SS-4),
every subsystem manifests ≥3 ways, every anchor character wants something
from another anchor.

- [ ] World identity + style bible in `world/canon/` (PR-2, DESIGN §8)
- [ ] 1 region: hex grid + terrain (reuse Barrowford demo geography or replace)
- [ ] Subsystems per the starter set in docs/SUBSYSTEMS.md §5: season clock
      + rumour network cores, ~5 interlocking setting instances, all passing
      the SS-14 checklist
- [ ] ~10 organisations
- [ ] ~50 characters: handful of anchors at T2 detail, rest as stubs
- [ ] ~8 places/POIs incl. rumoured-only ones (KN-5)
- [ ] Player character (twinned per LR-5) + starting knowledge file
- [ ] Minimal mythic layer: 1-2 traditions, one uncanny subsystem instance,
      rationalization rule exercised (LR-1..4; enables VA-7)
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
- [ ] VA-7 layer seam: mythic event, mixed witnesses, true vs rationalized
      accounts diverge and stay distinct (LR-7)
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

## M9 — First-run experiences and world publishing (OB-1..10, D-019)

Can begin once M1 (templates to generate into) and M3 (a loop to hand off
to) exist; independent of M5-M8.
**Exit:** a stranger can go template → world → character → first journey in
one sitting via the shallow path; a published world routes a second
stranger straight to character creation (OB-9 walked end to end).

- [ ] `/begin` lifecycle router (OB-1)
- [ ] `/worldbuild`: four-questions opening, archetype-library scaffold,
      depth dial with authorship provenance, re-enterable deepening
      (OB-2, OB-3)
- [ ] Minimum-viable-world validator (OB-4 as a checkable gate)
- [ ] `/newcharacter`: embedding flow — backstory NPCs, obligations,
      knowledge + false rumours, gift history, personal thread (OB-5..7)
- [ ] Publishing kit: checklist, `world/secrets/` layout, world release
      tagging, player-facing README template (OB-8, OB-10; TWIN-GUIDE §6)
- [ ] Player-door test: instantiate a published world, create character,
      play (OB-9)

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
- **Waterborne travel** (needed by twin #1, D-016): coastal/river/open-sea
  journey legs, vessels as a travel mode with their own risks and speeds,
  route planning across water hexes (extends /travel, M3-adjacent)
- **Dual naming / localization in UI**: alias place-names rendering (two
  names, one place), macron/diacritic-safe typography check in templates
  (UI-7, WB-2 aliases)
- **Worldbuilding layer, remaining scope** (D-014; core folded into M9 per
  D-019): guided canon *import* (bring an existing setting bible/notes into
  the schema), link-integrity sweeps at scale, worldgen quality passes
