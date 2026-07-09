# Decisions

Architecture Decision Records for the engine and game. Append-only: reversing
a decision requires a superseding entry (per spec CC-2), never deletion.
Format: context → decision → rationale → consequences.

---

## D-001 — World state lives in a markdown knowledge base, not a database

**Context.** Prior LLM-driven games (AI Dungeon lineage) collapse because the
world exists only in the model's context window: no ground truth, so the world
contradicts itself and player actions evaporate.

**Decision.** All world state is a structured markdown vault ("world brain")
with typed entities, stable IDs, YAML frontmatter, and typed wikilink
relationships — the master-brain pattern, adapted. The LLM is narrator,
adjudicator, and simulator; the vault is the truth.

**Rationale.** Externalized state is the fix for coherence drift. Markdown is
native territory for an LLM runtime, human-inspectable, diffable, and
versionable. The pattern is proven in production for business knowledge.

**Consequences.** Retrieval quality over ~10k files becomes a real concern
(index notes, later embeddings). Everything downstream (catch-up, provenance,
UI-as-pure-render) depends on the vault being the single source of truth.

## D-002 — Append-only event log as the spine; durable notes are views

**Decision.** Every consequential happening is an event record first; entity
files are materialized summaries over events (spec EV-*).

**Rationale.** Gives replay, debugging, provenance, and — critically — the
causal trail that catch-up narration (D-005) and NPC arcs (D-007) are
generated from. Directly transplanted from master-brain's capture-first rule.

## D-003 — Overlapping subsystems, not exclusive biomes

**Decision.** The simulation unit is the subsystem: state + dynamics +
footprint + couplings + manifestation layer. Footprints overlap geographic
areas freely; creatures are subsystem state, not entities (spec SS-*).

**Rationale.** Emergence lives in the interactions between systems sharing
ground. Exclusive biomes (Minecraft-style) make terrain the content; here
terrain is the stage. Also the cost model: hundreds of subsystems tick
affordably where thousands of individuals cannot.

**Consequences.** "Ecosystem" was renamed "subsystem" to shed the biology
connotation. Every area should sit in 2+ footprints (SS-4).

## D-004 — Foveated, tiered simulation (T0–T3)

**Context.** Thousands of characters cannot be LLM-ticked; the economics and
latency are impossible. Stanford's Generative Agents proved coherence at 25
agents; we need the *effect* at 1000×.

**Decision.** Aggregate simulation everywhere, always (subsystems); individual
detail only inside the fovea (player position + attention-hot threads).
Individuals exist at four fidelity tiers: statistic → sampled → persistent →
personal subsystem (spec SIM-*).

**Rationale.** Psychohistory + foveated rendering. Masses are predictable in
aggregate; detail is manufactured on observation and made canon after. This is
also how human DMs actually run worlds: sparse notes, improvised consistent
detail, written down once it happens. Precedent: STALKER's A-Life
online/offline split — our improvements are rich subsystem dynamics offline
and LLM retro-narration hiding the seam.

## D-005 — Catch-up by retro-narration, never replay

**Decision.** When the player returns after elapsed time, individual outcomes
are interpolated from accumulated subsystem deltas + events, then canonized
(spec SIM-7/SIM-8).

**Rationale.** Replaying fine ticks is unaffordable and unnecessary.
Interpolating human-scale stories from aggregate constraints is precisely what
LLMs are uniquely good at — this one mechanism is why the concept is viable
now and wasn't five years ago.

## D-006 — The identity ratchet

**Decision.** Once the player directly interacts with an individual, that
individual is permanent canon (T2+) and can never dissolve back into
statistics. Serialization compresses activity, never identity (spec SIM-3).

**Rationale.** The single cheapest honesty guarantee in the design: the world
must not gaslight the player about people they've met. A T2 stub costs almost
nothing to keep.

## D-007 — Consequence-weighted promotion; promoted NPCs are subsystems

**Decision.** Interaction threshold to T3 is scored by consequence (saved
lives, promises, debts, harm, attention), not contact count; promotion is
hysteretic; the promotion event records the player's causal fingerprint, which
seeds the NPC's arc. A T3 character is a subsystem in the standard five-part
sense — no special machinery (spec SIM-4, SS-3).

**Rationale.** Reuses the one abstraction we already have (elegance =
correctness signal). Arcs bent by the player's own deeds are the game's
emotional payload; the storyteller casts returning characters before
strangers. Roster growth is bounded by resolving arcs (dormancy), not
deleting people.

## D-008 — Prose by pipeline with authority, not multi-agent consensus

**Context.** Proposal was writer + art director + director agents "coming to
agreement" per beat.

**Decision.** A film-crew *hierarchy*: engine facts (non-negotiable) →
director brief (intent, incl. what to withhold) → one writer (voice) → editor
(canon, knowledge leaks, style drift; one revision max). Art direction is
durable canon (style bible, location canon, voice sheets) maintained offline,
not a per-beat debater (spec PR-*).

**Rationale.** Consensus among LLM agents averages voices into beige; film
crews don't actually work by agreement. Briefs solve blandness, authority
solves deadlock, offline canon solves cost. Production values are foveated:
full crew for big beats, writer-only for connective tissue — latency is the
frame rate.

**Consequences.** VA-4 requires a blind A/B (pipeline vs single-pass) before
the crew's cost is accepted as paid for.

## D-009 — Two-layer knowledge; subsystems never shown

**Decision.** World-truth and character knowledge are separate layers; nothing
reaches the player their character hasn't perceived. Subsystem names, states,
and footprints are engine-internal, full stop (spec KN-*).

**Rationale.** Without the split there is no mystery, deception, or discovery.
Learning "wolf country" from carcasses — not from a legend — is a core mastery
loop. Supersedes the map's v1 `overlays` field, which drew subsystem
footprints on the player's map and was removed deliberately (spec UI-5).

## D-010 — Hex crawl at The One Ring travel-phase altitude

**Decision.** The interface and decision altitude imitate TOR 2e's travel
phase — click destination, choose route and preparations, play authored
journey beats — borrowing the altitude, not the mechanics. Encounters are not
random tables; they are manifestations of the subsystem footprints the route
crosses (design §4).

**Rationale.** TOR's journey rhythm is the best-in-class compression of
travel into meaningful decisions. Sampling the living world improves on its
random tables, and route choice becomes (unlabeled) subsystem navigation —
strategy without WASD. Travel consuming world time couples the two clocks.

## D-011 — Claude Code is the engine runtime; no API key required

**Decision.** The prototype runs as a Claude Code project: vault = world,
skills = game loop, subagents = crew, chat = table. Graduation ladder: inline
widget UI → local web client in the preview pane (file-queue bridge) → Claude
Agent SDK app. World brain and skills stay identical across rungs (spec RT-4).

**Rationale.** Zero infrastructure between idea and playtest; every hour goes
into the actual hard problems (the VA seams). Local models (Ollama) are
explicitly not the DM — the design leans hardest on frontier-model strengths
(long-context consistency, adjudication judgment, retro-narration); Ollama may
later serve retrieval/classification.

## D-012 — Golden-path UI templates with byte-identical state injection

**Context.** LLM-generated UI drifts; the user wants authorable, consistent
UI they control.

**Decision.** Each widget type has one canonical human-owned template in
`ui/`; renders replace exactly `/*__STATE__*/null` with state JSON and change
nothing else. Templates are self-previewing (DEMO_STATE + sendPrompt shim).
Visual changes land in the template file only (spec UI-*).

**Rationale.** Consistency comes from the file, not the model's memory. The
template doubles as its own test harness; the user can author it in any
editor. Cost note: each render retransmits the template, so it stays lean.

## D-013 — Specs govern the work

**Decision.** ENGINE-SPEC.md (numbered requirements), DESIGN.md (experience),
DECISIONS.md (this file) are the source of truth. Docs-first change control;
implementations cite requirement IDs; IDs are never renumbered (spec CC-*).

**Rationale.** The runtime is an LLM reading files: here, spec quality *is*
engine quality, and session amnesia (AR-3) makes undocumented decisions
worthless. Fifteen-odd significant decisions existed only in one conversation
before this file.

## D-014 — Twin is a human-centric digital twin; the worldbuilding layer is the destination

**Context.** The project's founding motivation: explore whether agentic tools
and agentic knowledge bases can create genuinely new kinds of play — while
rejecting the default failure mode of generative AI in games, where the
machine improvises everything and produces worlds with no memory, stories
with no author, and choices with no weight.

**Decision.** Twin is named for what it is: a digital twin of the world in
the player's head. Human-centricity is a governing principle (design pillar
6): the human imagines the world, decides what matters, drives the story at
its turning points, and plays the character; the engine keeps the books,
simulates consequences, remembers everything, and plays everyone else. Public
release includes a worldbuilding layer through which players author their own
settings (places, people, tensions) and then inhabit them.

**Rationale.** The bookkeeping burden — consistent histories, hundreds of
people who stay themselves, time that really passes — is exactly what stops
humans from realising imagined worlds, and exactly what an agentic knowledge
base is good at. Splitting the work the way a great gaming table splits it
keeps the meaning human and the tedium mechanical.

**Consequences.** README leads with this thesis. Design pillar 6 gives it
veto power over feature decisions. The worldbuilding layer sits on the plan's
backlog, to be pulled into a milestone ahead of public release; authoring
ergonomics (schema simplicity, worldgen-with-review, canon inviolability)
become first-class engine concerns, not internal details.

## D-015 — Subsystem mechanics v1: ordinal state, damped propagation, mandatory legibility

**Context.** SS-1's five-part definition said what a subsystem is, not how it
behaves under simulation. Three predictable failure modes for LLM-driven
system simulation: numeric drift (invented precision, inconsistent
arithmetic), apocalypse spirals (runaway positive feedback — every famine
becomes an extinction in five ticks), and invisible machinery (state that
hurts the player without ever being perceivable, violating design pillar 5).

**Decision.** (ENGINE-SPEC SS-5..SS-15, docs/SUBSYSTEMS.md.)
1. State is typed, defaulting to 5-point ordinal levels; numbers only where
   the exact quantity is playable.
2. Propagation is damped by construction: coupling latency ≥ 1 tick, depth-1
   propagation per tick, one level-step per variable per tick unless a
   structural impact forces more. Opposing pushes hold steady and surface as
   storyteller tensions.
3. Legibility is mandatory: six manifestation channels, a fairness rule (no
   variable may bite before it is perceivable), and a per-subsystem
   signature so world-reading is a learnable player skill.
4. Baseline drift is mandatory — a system that only moves when pushed is a
   prop.
5. Scale classes with ordered ticking (world → regional → local), lifecycle
   status, in-file impact ledgers, and a hex→subsystem footprint index.
6. An engine-layer archetype library (docs/SUBSYSTEMS.md, ~80 templates in
   12 families) is the canonical source for instantiating subsystems; the
   rumour network and player renown are themselves subsystems, and the
   season clock is the master upstream system.

**Rationale.** Ordinal scales are robust where LLM arithmetic is not. Slow,
damped systems are both stable and dramatically legible — the player can see
trouble coming, which is where journey strategy lives. The fairness rule is
pillar 5 (honest world) applied to simulation. Modeling rumour and renown as
subsystems reuses the one abstraction instead of inventing side mechanisms
(same unification logic as D-007).

**Consequences.** Tick implementation (M3) becomes mostly mechanical rule
application. The catalog doubles as the worldbuilding layer's template
menu (D-014). VA-6 (entropy test) now has teeth: if an unattended year still
goes to mush, the fault is in dynamics authoring, not in unbounded feedback.

## D-016 — Engine/twin repository split; setting-agnosticism audited against the first twin

**Context.** The first world to be twinned is planned: Aotearoa New Zealand,
1827 — the Musket Wars era, a few hundred Europeans on the edge of a Māori
world — overlaid with sparse, rule-bound folk-horror fantasy and a
migration-of-gods premise (well-established Māori beings of the land
encountering the thin, hungry spirits arriving with British, Irish,
French, Chinese, and Pacific newcomers). Auditing the engine against this
setting exposed silent assumptions: European feudal furniture in the
catalog, an implicitly northern four-season calendar, a flat perception
model, a European-journal UI conceit, and no stated rule keeping setting
content out of the engine.

**Decision.**
1. **AR-4:** the engine repo ships no setting. Each twin is its own
   repository instantiating the engine; Barrowford is a disposable dev
   fixture. When a twin exposes a cultural/era assumption in the engine, the
   engine is fixed (this entry is the first application).
2. **TM-5:** calendars are setting data, including day-quality systems
   (lucky/unlucky/restricted days).
3. **SS-9 audience filters:** manifestations may be perception-gated (the
   sighted, the initiated) with KN-2 applied per audience — the generic
   mechanism behind hidden-world and second-sight play.
4. **UI-7:** golden-path templates are twin-skinnable (glyphs, terrain art,
   type, chrome language, dual naming); injection contract and STATE schema
   stay engine-governed.
5. **Catalog generalization:** feudal-specific names generalized to
   culture-neutral archetypes with peer variant forms; new "Contact and
   migration" family (cultural frontier, conversion contest, arms
   revolution, ecological invasion, migration and settlement, frontier
   entrepôt, kinship web, obligation economy, sacred restriction system);
   uncanny family gains belief economy, spirit road, hidden folk.

**Rationale.** "Setting-agnostic" is only testable against a concrete
non-default setting; NZ-1827 is maximally revealing because its systems
(chiefly authority, obligation economies, tapu-like restriction, contact
dynamics, spirit geography) are exactly what a Euro-medieval default
forgets. The supernatural overlay also validates the architecture: an
ecology of introduced beings colliding with endemic ones mirrors the
literal ecological invasion of the era — the same story at two altitudes,
which is what overlapping subsystems are for.

**Consequences.** The twin's content (places, beings, history, its
cultural-care policy for living Māori heritage — which that repo MUST carry
as a governing document) lives in its own repository, never here. Known
engine gap flagged, not yet solved: waterborne travel (coastal/vessel
journeys) — backlogged with this twin as motivating case.

## D-017 — Layered reality is the engine's fixed creative premise; the player is always twinned

**Context.** The first twin's strongest ideas — a plural, multicultural
mythic layer mirroring the mundane world's history of rootedness and
migration, and a player character who perceives both — proved to be
setting-portable structure rather than setting content. Meanwhile the
engine's public ambition (D-014: inspire people to build their own worlds)
needs a creative frame stronger than "anything pre-industrial": fully open
prompts paralyze; good constraints generate.

**Decision.** (Spec §16, LR-1..LR-7.) Every world has two coextensive
layers, mundane and mythic; mythic content is organized into plural,
culture-linked traditions (rooted or migrating with their peoples; new
entity kind `tradition`); the mythic layer runs on ordinary engine physics
with gated perception; mundane witnesses receive mythic events rationalized
(LR-4), and the rumour network carries the rationalized account; the player
character is always one of the **twinned**, living in both worlds. Twins
choose density, tone, naming, and the social price of the gift — never
whether the structure exists. VA-7 tests the layer seam.

**Rationale.** The constraint is generative, not prescriptive: it asks
every worldbuilder the same four fertile questions (whose land, who came,
what did they bring, who can see) and imposes no answer. It converts the
folk-horror, migrating-gods register from one twin's flavor into an engine
identity, gives the always-on investigative texture for free (the gap
between the true and rationalized accounts is playable content), reuses
existing machinery (SS-9 audiences, belief economy, KN layers) rather than
adding any, and completes the project's name: the digital twin, the twinned
world, the twinned player.

**Consequences.** README pitches the premise publicly. DESIGN vision and
player fantasy updated (0.3.0); uncanny family is no longer optional, only
tunable (SUBSYSTEMS 0.3.0). M1 gains a tradition template; the M2 fixture
must exercise the layer seam minimally. Settings that want *no* mythic
layer are out of scope for this engine — that is the one door deliberately
closed.

## D-018 — Open source under MIT; twins instantiate via GitHub template + engine remote

**Context.** The engine goes public on the author's personal GitHub
(github.com/DavidJCrawford) so others can play with it, build worlds, and
contribute. The first twin (1827 Aotearoa) must be a separate repository
created exactly the way a third party would create theirs (AR-4 made
operational).

**Decision.**
1. **License: MIT** (inbound = outbound for contributions). Rationale over
   Apache-2.0: minimal friction for a spec-and-markdown engine; over
   copyleft: worlds built with the engine must be unambiguously their
   makers' property, commercial or otherwise, and the license must never
   reach into setting content.
2. **Distribution: GitHub template repository.** A twin is created via "Use
   this template" — a clean snapshot with independent history — then adds
   the engine as a secondary git remote for tag-based upgrades (first merge
   `--allow-unrelated-histories`, ordinary merges thereafter). Rejected:
   submodules (break the CLAUDE.md-at-root session model, high friction)
   and forks (GitHub semantics fit contribution, not derivation).
3. **Governance for contributors** lives in CONTRIBUTING.md and mirrors
   internal rules: docs-first, requirement-ID citation, append-only
   decisions, deterministic art, culture-neutral archetypes, no setting
   content in the engine. AI-assisted contributions are explicitly welcome
   on equal terms.
4. **Engine releases are tagged**; twins record their engine baseline and
   upgrade deliberately. docs/TWIN-GUIDE.md is the canonical instantiation
   and upgrade procedure, including the first-session checklist and the
   cultural-care requirement for twins drawing on living cultures.

**Consequences.** The repo is public and template-flagged; the plan's
"twin instantiation guide" backlog item is done. The 1827 Aotearoa repo is
created from the template like any stranger's world would be — first
independent validation of the upgrade path design.

*Addendum (2026-07-10):* MIT confirmed after an explicit review of the
"others can monetize it" question. Accepted knowingly: rebrands and
commercial hosting are legal; judged low-risk for a spec-driven engine whose
value is the living project. The intended guards are name-level (a future
trademark position on "Twin") and open-core (engine open, future product
layers separate) — not license restrictions.
