# Claude Game Engine — specification

**Version:** 0.10.0 (2026-07-10)
**Status:** Draft — governs all work in this repository.

This document specifies a setting-agnostic game engine in which the world state
is a structured markdown knowledge base (the "world brain") and the runtime is an
LLM agent harness (Claude Code, later the Claude Agent SDK). The engine is
separate from any particular game world: the world brain's *schema and dynamics*
are engine; its *contents* are setting.

MUST/SHOULD/MAY are used per RFC 2119. Requirements carry stable IDs (`WB-1`,
`SIM-4`). **IDs are never renumbered or reused** — superseded requirements are
marked *withdrawn* in place; new requirements append. Cite IDs when implementing
or reviewing ("per SIM-3 ...").

---

## 1. Architecture overview

Three strictly separated layers:

| Layer | What it is | Where it lives |
| --- | --- | --- |
| Engine | Schema, simulation rules, agent roles, skills, UI contract | `docs/`, `.claude/`, `ui/` |
| Setting | The world brain content (entities, subsystems, events, canon) | `world/` |
| View | Rendered interfaces (map widget; later a web client) | generated at runtime from state |

- **AR-1** No truth may live in the view layer. Every pixel and every sentence
  shown to the player MUST be derivable from the world brain.
- **AR-2** Engine rules MUST NOT reference setting specifics (no place names,
  no genre assumptions beyond "characters, places, organisations, subsystems").
- **AR-3** The runtime is a Claude Code session opened on this repository. The
  session boots cold: all persistent knowledge MUST come from files, none from
  conversation memory.
- **AR-4** This repository is the engine and carries no shipped setting. Each
  world ("twin") lives in its own repository that instantiates the engine
  (docs, skills, UI templates) and supplies its own `world/`. Any world
  content in this repo (e.g. the Barrowford demo data in UI templates and
  fixtures) is a disposable development fixture, never canon. Engine rules
  and the archetype library MUST NOT assume any particular culture, era
  aesthetic, hemisphere, or cosmology beyond "pre-industrial, human-scale"
  (DESIGN §1); when a twin exposes such an assumption, fix the engine.

## 2. Glossary

- **World brain** — the markdown vault holding all world state.
- **Canon** — established world facts. Once written, canon is not silently
  rewritten (EV-4).
- **Subsystem** — a named dynamic system with state, dynamics, footprint,
  couplings, and a manifestation layer (SS-1). The universal simulation unit.
- **Footprint** — the geographic areas a subsystem touches. Footprints overlap.
- **Manifestation** — how a subsystem shows up at human scale when observed.
- **Fovea** — the set of locations/entities currently simulated at full detail:
  the player's position plus attention-hot threads (SIM-5).
- **Fidelity tiers (T0–T3)** — statistic → sampled individual → persistent
  individual → personal subsystem (SIM-1).
- **Tick** — offline advancement of world time at aggregate level (TM-2).
- **Catch-up** — retro-narration of off-screen change from subsystem deltas
  when the fovea returns somewhere (SIM-7).
- **Crew** — the prose pipeline roles: director, writer, editor, art director
  (PR-1).
- **Golden path** — a human-owned UI template rendered verbatim with state
  injected (UI-1).
- **Thread** — an active narrative situation tracked by the storyteller.
- **Layers** — the two coextensive realities of every world: the **mundane
  layer** (material, historical) and the **mythic layer** (beings, forces,
  observances — real, but veiled from most inhabitants) (LR-1).
- **Tradition** — a culture-linked body of mythic beings and practices;
  rooted in a land or migrating with a people (LR-2).
- **The twinned** — those who live in both worlds and perceive both layers.
  The player character is always one of them (LR-5). Each twin names its
  own form of the gift.

## 3. World brain (WB)

- **WB-1** The world brain lives in `world/` as markdown files with YAML
  frontmatter. Layout:

```
world/
├── meta/            # world identity, authoring notes (designer-facing)
├── areas/           # geographic areas; each defines its hex grid
├── places/          # named places (POIs and finer-grained locations)
├── characters/      # T2+ individuals only (see SIM-2); tier in frontmatter
├── organisations/
├── subsystems/      # one file per subsystem
├── threads/         # active narrative threads (storyteller-owned)
├── events/          # append-only event log (see EV)
├── knowledge/       # per-character knowledge overlays; at minimum player.md
├── canon/           # prose-canon artifacts: style bible, location canon, voice sheets
└── state/           # hard state: clock, player position/resources, explored map
```

- **WB-2** Every entity file MUST have frontmatter with at least: `type`, `id`
  (stable, immutable, never reused), `created`, `updated`. Relationships are
  typed frontmatter fields containing quoted wikilinks, not prose-only mentions.
- **WB-3** Entity kinds: `area`, `place`, `character`, `organisation`,
  `subsystem`, `thread`, `event`, `knowledge`, `canon`, `tradition` (LR-2).
  New kinds require a spec change first.
- **WB-4** Hard state (time, position, health, inventory, money, counts,
  quantities) MUST be stored as structured YAML values, never only as prose.
  Prose describes; YAML measures.
- **WB-5** Files MUST NOT be deleted or renamed during play. Retirement is a
  status change (`status: dormant | dead | archived`), preserving links.
- **WB-6** Provenance: any durable claim SHOULD link to the event(s) that
  established it.

## 4. Event log (EV)

- **EV-1** The event log is the spine of the simulation: every player action
  outcome, every tick's notable happenings, every promotion/demotion, every
  subsystem impact MUST be recorded as an event before or with the durable-note
  updates it causes. Durable notes are materialized views over events.
- **EV-2** Events are append-only. An event, once written, is never edited
  except to fix mechanical link errors. Corrections are new events.
- **EV-3** Event frontmatter MUST include: `id`, `when` (in-world date),
  `where` (area/place links), `actors` (character/organisation links),
  `impacts` (list of `{subsystem, magnitude}` — see AD-4), and `visibility`
  (who could know: `public | local | witnessed:[links] | secret`).
- **EV-4** Conflict rule: when new information contradicts existing canon, the
  engine MUST NOT silently overwrite. Record the new claim, link both, and flag
  the contradiction for resolution (a continuity issue, or a deliberate in-world
  mystery).

## 5. Knowledge model (KN)

- **KN-1** World-truth and character knowledge are separate layers. The world
  brain is the DM's notebook; `world/knowledge/<character>.md` records what a
  given character (including the player's) knows, believes, and misbelieves.
- **KN-2** Nothing may be shown to the player — in prose, map, chips, actions,
  or tooltips — that their character has not perceived, learned, or inferred.
  Event `visibility` (EV-3) drives what flows into knowledge files.
- **KN-3** Subsystems are never shown. Subsystem names, states, and footprints
  are engine-internal. They MUST NOT appear in the UI or be named as systems in
  prose. The player experiences subsystems only through manifestations:
  encounters, rumours, prices, weather, the state of the land.
- **KN-4** False beliefs are first-class: knowledge files MAY contain claims
  that contradict world-truth, with the source of the misbelief linked.
- **KN-5** Rumours are knowledge entries with `confidence: rumour`. The map may
  show rumoured places (faded) per the UI contract.

(The layered-reality premise, §16, builds on this model.)

## 6. Subsystems (SS)

- **SS-1** A subsystem is defined by exactly five parts:
  1. **State** — YAML variables (pressures, stocks, moods, positions).
  2. **Dynamics** — plain-language update rules applied at each tick.
  3. **Footprint** — the areas/hexes it touches. Footprints of different
     subsystems MUST be allowed to overlap; there is no exclusive-biome rule.
  4. **Couplings** — named links to other subsystems with the direction and
     nature of influence.
  5. **Manifestation layer** — how it shows up when the fovea lands on its
     footprint: NPC archetypes, scene dressing, encounter hooks, price/rumour
     effects, keyed to state levels.
- **SS-2** Creatures and animals are not entities; they are terms in subsystem
  state ("wolf pressure: high"). A specific animal is instantiated only as
  scene detail via manifestation, and does not persist unless the encounter
  makes it canon-worthy.
- **SS-3** A promoted character (T3) is a subsystem in the SS-1 sense whose
  file lives with the character (SIM-4). Same five parts, small mobile
  footprint, coupling to the player-thread mandatory.
- **SS-4** Every area SHOULD sit inside multiple footprints. An area with only
  one subsystem is a design smell (nothing can interact).
- **SS-5** State variables are typed: `level` (5-point ordinal: dormant, low,
  moderate, high, critical — the default), `stock` (number + unit, only where
  exact quantity is playable), `flag`, `clock` (fires on an in-world date),
  `extent` (area/hex list). Prose MUST NOT carry a value the YAML doesn't.
  2–5 variables per subsystem.
- **SS-6** Dynamics comprise three groups, all in plain language: baseline
  drift (MANDATORY — what happens with no inputs), conditional rules, and
  threshold events (level transitions that emit events with `visibility`).
- **SS-7** A coupling is `{from: subsystem.variable, to: variable, sense: +/−,
  strength: weak|strong, latency: ≥1 tick, condition?}`. Latency is never
  zero; a tick applies direct couplings only (depth-1 propagation — knock-on
  effects land on subsequent ticks).
- **SS-8** Damping: a `level` variable moves at most one step per tick
  regardless of how many couplings push it, unless a `structural` impact
  (AD-4) forces more. Opposing pushes hold the variable steady and MUST be
  flagged to the storyteller as a tension (conflicts are content, not
  errors).
- **SS-9** Manifestations are keyed by variable level across six channels:
  `encounter`, `rumour` (with a truth flag), `price`, `sensory`, `npc`,
  `travel`. Fairness rule: every variable that can affect the player MUST
  have at least one perceivable manifestation before it bites. Each
  subsystem defines a `signature` — how an observer distinguishes its
  fingerprints from a neighbour's. A manifestation MAY carry an `audience`
  filter (e.g. the sighted, the initiated, children, the dying): only
  characters matching it perceive that manifestation, and KN-2 applies per
  audience. Audience-gated subsystems still satisfy the fairness rule if at
  least one manifestation per biting variable is perceivable by *someone*
  the player could plausibly consult.
- **SS-10** `scale: world | regional | local`. World subsystems tick
  seasonally, regional weekly-to-monthly, local weekly. Ticks process
  world → regional → local.
- **SS-11** `status: nascent | active | dormant | resolved`. The storyteller
  MAY spawn subsystems from archetype templates (a plague arrives). Resolved
  subsystems keep their files (WB-5).
- **SS-12** Each subsystem file carries a `ledger:` list where sub-`notable`
  impacts (AD-5) accrue with dates; the tick MAY convert accumulated ledger
  weight into variable steps.
- **SS-13** `world/state/footprints.md` maps hex → subsystem ids and MUST be
  rebuilt whenever any footprint changes. Manifestation sampling and route
  planning read this index, not the whole vault.
- **SS-14** A subsystem enters play only if it passes the authoring checklist
  in `docs/SUBSYSTEMS.md` §4 (all parts present, drift defined, ≥2 couplings
  with ≥1 outgoing, overlap satisfied, ≥3 manifestations on ≥3 channels,
  fairness rule, signature, scale/status/ledger set).
- **SS-15** `docs/SUBSYSTEMS.md` is the engine-layer archetype library
  (setting-agnostic templates per AR-2) and the mechanics companion to this
  section. Settings instantiate archetypes; novel archetypes SHOULD be added
  to the library, not invented ad hoc in world files.

## 7. Simulation fidelity (SIM)

- **SIM-1** Four tiers:
  - **T0 statistic** — exists only inside subsystem state.
  - **T1 sampled** — instantiated by manifestation while observed; MAY evaporate
    back to T0 if the player never meaningfully interacts.
  - **T2 persistent** — the player has directly interacted: permanent canon
    (name, record, memory of the encounter). Cheap stub; not ticked.
  - **T3 personal subsystem** — crossed the interaction threshold: own state,
    dynamics, and arc; advanced by ticks whether or not observed.
- **SIM-2** `world/characters/` holds T2+ only. T0/T1 individuals MUST NOT get
  files; that is what keeps thousands of souls affordable.
- **SIM-3** The identity ratchet: T2+ is permanent. A character the player has
  interacted with MUST never dissolve back into statistics, and on re-encounter
  MUST be the same person, consistent with their record and elapsed events.
- **SIM-4** Promotion T2→T3 is scored by consequence, not contact count: saved
  or ruined lives, promises, debts, harm, and strong player attention (naming,
  gifts, repeated seeking-out) count; small talk does not. Promotion MUST be
  hysteretic (hard to earn, slow to decay) and MUST be recorded as an event
  carrying the causal fingerprint — *why this person now matters* — which seeds
  the T3 arc's initial state.
- **SIM-5** The fovea is multi-focal: the player's location plus entities on
  attention-hot threads (a rival's plot, an ally on a mission, a letter in
  transit). Proximity is the default attention signal, not the only one.
- **SIM-6** De-instantiation: when the fovea leaves, detailed activity is
  summarized into the character/place record and control returns to subsystem
  statistics. Serialization compresses activity, never identity (SIM-3).
- **SIM-7** Catch-up: when the fovea returns after elapsed time, the engine
  MUST NOT replay fine-grained ticks. It takes the accumulated subsystem deltas
  and relevant events and retro-narrates individual outcomes consistent with
  them. Retro-narrated outcomes become canon (events + record updates).
- **SIM-8** Retro-consistency: any newly instantiated detail MUST be consistent
  with current subsystem state, prior canon, and recorded events.
- **SIM-9** Arc dormancy: the storyteller SHOULD resolve T3 arcs (settled,
  avenged, dead, moved on), demoting the character to T2 — still themselves,
  no longer ticked. The concurrent T3 roster is a budget (cast size), enforced
  by resolving arcs, not by deleting people.

## 8. Time and ticks (TM)

- **TM-1** The world clock (`world/state/clock.md`) advances only through
  engine operations: travel, rest, waiting, and explicit ticks. Player time and
  world time are coupled through distance.
- **TM-2** The offline tick advances subsystems at aggregate level: apply each
  subsystem's dynamics, propagate couplings, advance T3 arcs, emit events, and
  update thread states. Ticks MUST NOT simulate T0–T2 individuals.
- **TM-3** Tick granularity is coarse (days to weeks per tick) and MAY be run
  in bulk ("tick until the player's arrival date").
- **TM-4** Every tick MUST leave an event trail sufficient for later catch-up
  narration (SIM-7) — deltas without events are not auditable.
- **TM-5** Calendars are setting data, defined in the twin's world brain:
  the number and names of seasons, month/day reckoning, festival days, and
  any day-quality system (calendars that mark days lucky, unlucky, or
  restricted for activities are calendars with gameplay and MUST be
  supported). The engine and the season-clock archetype MUST NOT assume four
  European seasons, any hemisphere, or any month names.

## 9. Adjudication (AD)

- **AD-1** Hard state changes (WB-4 values) are computed deterministically by
  skills/rules where a rule exists. The LLM adjudicates only the open-ended
  residue, constrained by hard state. Numbers in YAML, drama in prose.
- **AD-2** The adjudicator MUST refuse physically/characterally impossible
  actions in-fiction (narrate the attempt failing or being impossible), not by
  breaking voice.
- **AD-3** Freeform player input is interpreted charitably for intent but
  adjudicated strictly for effect. Persuasion of NPCs is judged against the
  NPC's state, knowledge, and interests — not against the player's rhetoric
  alone (munchkin resistance).
- **AD-4** Every adjudicated outcome MUST emit graded subsystem impact events:
  `{subsystem, magnitude: none|minor|notable|major|structural}`. Killing one
  grain merchant is `minor` to a famine; burning the granary is `major`.
  Magnitude vocabulary is fixed by this spec.
- **AD-5** Impacts below `notable` MUST NOT move subsystem state directly; they
  accumulate in the subsystem's ledger and MAY tip state at tick time.
  (Prevents both futility and chaos-oversensitivity.)

## 10. Narrative direction (ND)

- **ND-1** A storyteller (director-level) role maintains narrative pressure:
  it owns `world/threads/`, seeds tension from subsystem states, and ensures
  unattended simulation trends toward story, not entropy.
- **ND-2** The storyteller casts from the T2/T3 roster before inventing
  strangers. Returning characters beat new ones.
- **ND-3** Threads have `heat` (attention level, feeds SIM-5) and `status`.
  The storyteller reviews threads at each tick.
- **ND-4** The storyteller MAY schedule futures ("the debt comes due in
  spring") as dated pending events, which ticks then fire.

## 11. Prose pipeline (PR)

- **PR-1** Prose is produced by a pipeline with authority, not consensus:
  1. **Facts** — the engine supplies what happened and what is perceivable
     (KN-2 filtered). Non-negotiable.
  2. **Director brief** — intent for the beat: purpose, tone, pacing, what to
     emphasize, what to withhold. A few lines, not prose.
  3. **Writer** — exactly one agent writes the text from brief + facts + canon.
  4. **Editor** — checks against canon and knowledge (no contradictions, no
     leaks per KN-2, no style drift, no AI-prose tells). One revision loop
     maximum; then the director's cut ships.
- **PR-2** Durable prose-canon artifacts live in `world/canon/`:
  - **Style bible** — the world's voice: register, imagery rules, what rain
    smells like here.
  - **Location canon** — per-place established sensory detail, written at first
    manifestation, extended thereafter. The same tavern is always the same
    tavern.
  - **Voice sheets** — per T2+ speaking character: diction, rhythm, tics,
    things they would never say. Created at promotion (casting).
- **PR-3** Foveated production values: full crew (brief + write + edit) for
  scene transitions and dramatic beats; skeleton crew (writer only, standing
  brief, cached canon) for connective dialogue. Latency is the frame rate.
- **PR-4** Canon artifacts are maintained offline (at first-dress, at ticks
  that change places), not while the player waits.

## 12. UI contract (UI)

- **UI-1** Golden path: every widget type has exactly one canonical,
  human-owned template file in `ui/`. Widgets are NEVER improvised. To render:
  read the template, replace the exact token `/*__STATE__*/null` with a JSON
  state object, change nothing else, ship byte-identical otherwise.
- **UI-2** Templates MUST be self-previewing: opened directly (no injection),
  they fall back to a built-in `DEMO_STATE` and a console `sendPrompt` shim.
- **UI-3** Template changes happen only in the template file, only on the
  user's request, never inline in a render. New STATE fields are added to the
  template renderer + DEMO_STATE + this spec before first use.
- **UI-4** The map STATE schema (v3) for `ui/travel-map.html`:

```
{
  location: string,            // header title
  date: string,                // in-world date line
  weather: string,             // one evocative phrase
  grid: {
    cols: int, rows: int,
    terrain: string[rows],     // per hex: p plains, f forest, F dense
                               // forest, h hills, m mountains, s marsh,
                               // w water, d desert, v village, c city
                               // (a city may span multiple contiguous
                               // hexes). Chars are case-sensitive.
    explored: "all" | ["r,c", ...],
    rivers: [{ r, c, k, rot, flip }]
                               // optional river overlay tiles, drawn on
                               // explored hexes only. k: p2p (straight,
                               // corner to corner) | e180 | e120 | e60
                               // (edge to edge, by bend angle) | src
                               // (source: edge to hex centre, tapering —
                               // a highland spring on mountain hexes, a
                               // lowland stream head anywhere else).
                               // rot 0-5 in 60° clockwise steps; flip
                               // mirrors before rotation. The kinds ×
                               // rot × flip compose any river course.
    roads: [{ r, c, k, rot, flip }]
                               // optional road overlay tiles; same tile
                               // grammar and transforms as rivers, but
                               // gentler curves and src means terminus
                               // (road dead-ends mid-hex at what it was
                               // built to reach). Drawn as a dotted track
                               // above rivers (crossings read as fords or
                               // bridges), explored hexes only.
    coasts: [{ r, c, style, edges }]
                               // optional shoreline styling on LAND hexes.
                               // style: beach (sand band, stipple, broken
                               // back-of-beach line) | cliff (bold wobbled
                               // line with seaward hachures). edges: list
                               // of edge indices 0-5 (0=E, 1=SE, 2=SW,
                               // 3=W, 4=NW, 5=NE) — explicit, so any
                               // subset works and no rotation is needed.
                               // Composes with any terrain (forest
                               // headlands, village beaches). Complements
                               // the automatic water-side shore hatching,
                               // which is always drawn. Explored hexes
                               // only.
  },
  pois: [{ r, c, name, glyph, known }],   // glyph: town|keep|mine|ruin|site
                                          // known: visible by rumour in fog
  player: { r, c },
  route: null | [[r,c], ...],
  stats: [{ label, value }],   // ~4 chips max; character-known facts only
  actions: [{ label, prompt }] // contextual choices; prompt sent verbatim
}
```

  Coordinates are odd-r offset, row-major, 0-indexed; player-facing hex names
  are column letter + row number (A1 top-left). Widget title: `travel_phase_map`.
- **UI-5** Everything in STATE is player knowledge (KN-2). There is no overlays
  field and none may be reintroduced (KN-3); v1's `overlays` is withdrawn.
- **UI-6** The map is re-rendered after any turn that changes position, route,
  explored hexes, date/weather, stats, or available actions.
- **UI-7** Skinning: a twin MAY re-skin its copy of a golden-path template —
  POI glyph set, terrain palette and art, typography, chrome language, and
  dual/alias place-naming — to fit its world's artifact conceit (DESIGN §9's
  parchment journal is the engine default, not a rule). The injection
  contract (UI-1..UI-3) and the STATE schema (UI-4) remain engine-governed;
  a twin that needs new STATE fields proposes them here first (CC-1).

## 13. Runtime and skills (RT)

- **RT-1** Prototype runtime is a Claude Code session on this repo. Every
  session MUST re-enter the world through files (AR-3): read `CLAUDE.md`, this
  spec on demand, and `world/state/` before acting.
- **RT-2** The game loop is implemented as skills (planned):
  - `/begin` — lifecycle router (OB-1): worldbuilding, character creation,
    or play, decided from repo state.
  - `/worldbuild` — the worldbuilding experience (OB-2..OB-4).
  - `/newcharacter` — the character creation experience (OB-5..OB-7).
  - `/travel <destination>` — plot candidate routes with differing subsystem
    exposure, present embarkation choices, run journey beats.
  - `/tick [until <date>]` — offline advancement per TM-2.
  - `/scene` — play out the current location.
  - `/worldgen` — authoring-time content generation (setting work, not play).
  Skill definitions live in `.claude/skills/` and MUST cite the spec sections
  they implement.
- **RT-3** Crew roles (PR-1) and the storyteller (ND-1) run as subagents with
  role prompts stored in the repo, not improvised per session.
- **RT-4** Graduation ladder: (1) chat widget UI — current; (2) local web
  client in the preview window bridged by a file-based action queue; (3)
  standalone app on the Claude Agent SDK. The world brain and skills MUST
  remain identical across all three (AR-1 makes this possible).

## 14. Validation (VA)

Acceptance tests for the pocket-world prototype. The engine is not "working"
until these pass; they are the seams most likely to fail.

- **VA-1 Catch-up seam.** Interact somewhere, travel away, tick 3+ in-world
  months, return. The catch-up narration must be consistent with subsystem
  deltas and prior canon, contradiction-free, and read as story, not changelog.
- **VA-2 Impact seam.** Perform a `minor` and a `major` action against the same
  subsystem (AD-4 example). Verify the minor accrues to the ledger without
  moving state, the major moves state at the next tick, and both surface later
  as in-world manifestations the player can perceive.
- **VA-3 Arc seam.** Promote an NPC via a consequential interaction, leave,
  tick months, return. Their offline arc must visibly descend from the
  player's causal fingerprint (SIM-4), not generic drift.
- **VA-4 Prose seam.** Blind-compare full-pipeline vs single-pass prose on ~20
  identical beats. Pipeline must win on continuity and restraint or be revised.
- **VA-5 Consistency soak.** A 50-turn session followed by a continuity sweep
  (contradictions, broken links, knowledge leaks). Zero KN-2 leaks tolerated;
  contradictions triaged per EV-4.
- **VA-6 Entropy test.** Tick one unattended in-world year. The storyteller
  must produce threads worth playing on return; if the world reads as mush,
  ND-* needs work before scaling content.

## 15. Change control

- **CC-1** Docs-first: engine behaviour changes land in this spec before or
  with their implementation. If play reveals the spec is wrong, the spec is
  amended (version bump + DECISIONS.md entry), not silently ignored.
- **CC-2** `docs/DECISIONS.md` records the rationale for significant choices.
  Reversing a recorded decision requires a superseding entry, not deletion.
- **CC-3** Semantic versioning of this spec: patch = clarification, minor =
  additive requirement, major = breaking change to schema or contracts.

## 16. Layered reality (LR)

The engine's one fixed creative premise (D-017). It is structural, not
prescriptive: it constrains the *shape* of every world, never its cultures,
era, tone, or content.

- **LR-1** Every world has two coextensive reality layers: the **mundane
  layer** (the material, historical world) and the **mythic layer** (beings,
  forces, and observances that are real within the world but veiled from
  most of its inhabitants). A twin chooses the mythic layer's density and
  tone — folk-horror sparse to mythic-age thick — not whether it exists.
- **LR-2** Mythic content is organized into **traditions**: culture-linked
  bodies of beings, practices, and old bargains. `tradition` is an entity
  kind (WB-3); every mythic entity and subsystem links to at least one.
  Traditions are plural by design: multiple traditions MAY overlap any
  geography. Rooted traditions belong to peoples of the land; migrant
  traditions travel with their peoples (the belief-economy archetype is the
  standard coupling; beings arrive with migrants, thin and hungry).
- **LR-3** Entities and subsystems declare `layer: mundane | mythic | both`.
  The mythic layer obeys the same engine physics as everything else —
  subsystems, couplings, events, canon; it holds no special machinery, only
  gated perception (LR-4).
- **LR-4** The rationalization rule. Mythic manifestations are
  audience-gated (SS-9). Characters without the gift perceive them
  *rationalized into mundane terms* — the drowned man, the dogs that ran
  off, the fever — and the rumour network (KN-5) carries the rationalized
  account. The twinned perceive truly. Both versions flow through KN-2 per
  audience; the gap between them is playable content.
- **LR-5** The player character is always one of the **twinned**: someone
  who lives in both worlds and perceives both layers. This is engine-level
  premise, not a twin option. Each twin names and prices the gift in its own
  terms (what it is called, how it is regarded, what duties it carries);
  the engine only guarantees the perception. The player's knowledge file
  records both layers; the map and UI may show mythic knowledge the
  character has earned, under the ordinary KN rules.
- **LR-6** Fairness crosses layers: SS-9's fairness rule applies to mythic
  variables. A mythic threat MUST be perceivable before it bites — to the
  twinned directly, or through rationalized signs a mundane observer could
  report.
- **LR-7** Validation: **VA-7 (layer seam).** Stage a mythic event before
  mixed witnesses. Verify the player's knowledge records the true account,
  NPC knowledge and the rumour network carry a rationalized one, the two
  accounts diverge plausibly, and later canon keeps them distinct (EV-4
  preserves the tension rather than merging it).

## 17. First run and world lifecycle (OB)

A twin serves two audiences: worldbuilders and players. The same repository
shape supports both through lifecycle routing and two template levels
(engine → world → playthrough). (D-019.)

- **OB-1** Lifecycle routing. Session boot reads repo state and routes:
  no world identity → the worldbuilding experience; world passes OB-4 but
  no player character exists → character creation; both exist → play
  (RT-1). The `/begin` skill embodies this router. No configuration — the
  files are the state (AR-3).
- **OB-2** The worldbuilding experience opens with the premise questions
  (§16): *whose land is this, who came, what did they bring, who can see?*
  It builds outward from the answers, scaffolded by the archetype library
  (SS-15) and the TWIN-GUIDE first-session checklist.
- **OB-3** The depth dial. Every creation prompt MUST accept three
  responses: **author** (the human supplies it), **propose** (the engine
  offers options to pick from and edit), **delegate** (the engine
  generates). Generated and unedited-proposed content records authorship
  provenance in frontmatter (`authorship: authored | edited | generated`);
  generated content remains marked until the human revisits and adopts it.
  This is pillar 6 under generation: the pen is offered back, always.
  Creation is re-enterable — a shallow pass MAY be deepened later, per
  entity or per area.
- **OB-4** Minimum viable world. Play cannot begin until the world has:
  identity (SETTING equivalent); a calendar (TM-5); one region with map
  data (UI-4); the two core subsystems plus ≥3 setting subsystems, all
  passing SS-14; ≥2 traditions, at least one rooted (LR-2); the gift named
  and priced (LR-5); a starting settlement; and ≥2 seeded threads (ND-3).
  The worldbuilding experience MUST close every gap before play — by
  authorship or by delegation, never by absence.
- **OB-5** Character creation. The character is one of the twinned (LR-5,
  non-negotiable) and of ordinary scale (DESIGN §12). There are no stat
  systems; capability lives in fiction and hard state (AD-1).
- **OB-6** Character creation is embedding. Its outputs are world files:
  the player character; starting knowledge including rumours, some false
  (KN-1, KN-5); 2–3 relationship NPCs at T2 born from backstory (SIM-1);
  obligations wired as subsystem ledger hooks (SS-12); the gift's personal
  history as the mythic knowledge seed; a starting location; and ≥1
  personal thread. Backstory enters canon through events (EV-1) like
  everything else.
- **OB-7** The depth dial (OB-3) applies to character creation identically,
  including authorship provenance.
- **OB-8** Publishing. A twin MAY be published as a playable world. The
  publishing checklist: OB-4 satisfied; SS-14 across all subsystems; the
  care policy finalized where TWIN-GUIDE requires one; a player-facing
  README stating what the world is, its tone, and content notes; spoiler
  layout per OB-10. The publisher tags a world release (`world-v1`, ...)
  and marks the repository a GitHub template — the engine's own
  distribution mechanism (D-018), one level up.
- **OB-9** Player instantiation. A repo created from a published world
  template routes (OB-1) directly to character creation. The player's repo
  is a divergent timeline of the world as of that release — their canon,
  their save, theirs to keep or delete. It records provenance: world
  release + engine version. Multiplayer remains out of scope (DESIGN §12):
  one repo, one timeline, one player.
- **OB-10** Spoiler convention. Published worlds keep world-truth
  mysteries in a clearly signposted location (`world/secrets/` by
  convention) so a player can choose not to read past the screen. This is
  a layout convention and an honor system, not enforcement — the same
  contract as not reading the gamemaster's notebook.
