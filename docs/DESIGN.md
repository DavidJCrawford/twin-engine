# Design

**Version:** 0.2.0 (2026-07-10)
**Scope:** The player experience. The engine spec (`ENGINE-SPEC.md`) says what
the machine does; this says what it should feel like and why. Where they seem
to conflict, surface it — don't pick silently.

---

## 1. Vision

A single-player, text-first journey game set in a living world that runs
whether or not you are watching. The world is simulated as hundreds of
overlapping subsystems — ecologies, economies, faiths, feuds, weathers — and
the player is the one statistically impossible individual moving through it:
a world of psychohistory, and you are the Mule. Play happens at the altitude
of a great tabletop campaign's travel phase: a map, a road, weather, company,
and consequences that are still there years later.

The project's name states its destination: a **digital twin of the world in
the player's head** (D-014). The engine exists to make a human's imagined
world real at a scale imagination alone can't sustain — the human supplies
meaning and direction; the machine supplies simulation, memory, and
consequence. At public release this becomes explicit as a worldbuilding
layer through which players author their own settings before inhabiting them.

## 2. Pillars

Every design question gets tested against these five. If a feature serves none
of them, it goes.

1. **The world is the protagonist.** It has its own momentum; it does not wait
   for you, orbit you, or reset for you. Returning somewhere after a year
   should feel like returning somewhere after a year.
2. **Journey altitude.** The player makes narrative decisions at meaningful
   moments — route, camp, parley, promise — never twitch inputs. One Ring
   travel phase, not open-world WASD.
3. **Language is the graphics engine.** Prose quality is not polish; it is the
   entire rendering budget. Restraint, consistency of imagery, and voice do
   the work textures do elsewhere.
4. **Consequence is the reward loop.** No XP, no levels. What the game pays
   out is: the world visibly bent by what you did — people whose stories carry
   your fingerprints, prices and roads and reputations that remember you.
5. **An honest world.** No invisible hand rewrites canon behind you. People
   you've met stay real (the identity ratchet). Mysteries have answers written
   down before you find them.
6. **The human holds the pen.** Twin amplifies human authorship; it never
   replaces it. The player's deeds are the strongest force in the world; the
   worldbuilder's canon is inviolable; the engine does the bookkeeping and
   plays everyone else. When a design choice trades human authorship for
   machine convenience, authorship wins.

## 3. The player fantasy

You inhabit one person of ordinary scale in an enormous, indifferent,
knowable world. The fantasy is not power; it is *presence and consequence* —
being genuinely somewhere, choosing a road, and mattering to the people and
places along it. The fiction of the interface: the map is your character's
own journal — parchment, ink, gaps, and rumours — not a satellite view.

## 4. The core loop

**Session loop:** arrive somewhere → learn what's changed (catch-up through
in-world channels: news, notices, gossip, the state of the streets) → act in
place (`scene` play) → choose a destination → travel → arrive.

**Journey loop (the heart of the game):**
1. Click a destination on the hex map.
2. The engine proposes 1–3 candidate routes. Routes differ in time, terrain,
   and — unstated — which subsystem footprints they cross. The player sees
   in-world signals ("the road through the marsh is faster, but Berrin spits
   when you mention it"), never system names.
3. Embarkation choices: preparations, company, intentions.
4. The journey runs as a handful of authored beats, not per-hex events. Most
   hexes pass in summary; the director places beats where subsystem pressure
   and hot threads make something worth playing. The player acts through each
   beat in freeform text.
5. Arrival: time has passed, the world has ticked, the map has grown.

**Encounter beat anatomy:** situation (from a manifestation) → the player
writes → adjudication → outcome prose → impacts recorded. A beat should
usually be resolvable in 1–4 exchanges; the journey keeps moving.

## 5. Agency and legibility

- Player actions register on a graded scale (engine: AD-4/AD-5). Design
  intent: **futility and chaos are both failures.** Small deeds accumulate;
  large deeds move the world; the player can *feel* the difference.
- Consequences surface diegetically and preferably with faces: the freed
  prisoner preaching your name, the ruined merchant turned rival. People are
  how the player reads their own impact.
- The world never explains itself in system terms. Players learn "wolf
  country" from carcasses and nervous drovers, and that knowledge — earned,
  fallible, mapped as rumour — is a core mastery loop.

## 6. Knowledge and discovery

- The map begins mostly fog. Fog is honest emptiness: blank parchment, not
  greyed-out content teasers.
- Rumoured places render faded before they're visited — the map records what
  your character has *heard*, not what is true.
- Wrong beliefs are playable content, not bugs: a rumour can mislead, a
  merchant can lie, and the map faithfully records the lie until experience
  corrects it.

## 7. Time

Two clocks, coupled by distance: your time (beats, scenes) and the world's
time (ticks). Travel is the exchange rate. Long roads mean a changed world at
the far end — which makes distance meaningful, news valuable, and promises
time-weighted ("come back in spring" is a real cost).

## 8. Prose voice

- **Register:** grounded, sensory, economical. Closer to Le Guin and Rosemary
  Sutcliff than to epic bombast. The style bible (engine: PR-2) is the
  enforceable version of this paragraph.
- **Withholding is a technique:** the director's "what not to say" instruction
  is where dread, mystery, and pace live. Never narrate what the character
  doesn't perceive; rarely narrate everything they do.
- **Consistency beats novelty:** the same tavern smells the same twice. Named
  people speak in their own cadence every time (voice sheets).
- **The player's words matter:** quote or honour the player's phrasing in
  outcomes where possible; it makes their authorship visible.

## 9. Visual language

- **The artifact conceit:** every UI surface is something that could exist in
  the world — a journal page, a map, a letter. No HUDs, no minimaps, no
  floating health bars.
- **Palette:** parchment (#F0E6CF family), sepia ink (#3B3020), dried-red ink
  (#8E3B2F) reserved for the player's own marks (position, route).
- **Type:** IM Fell English / IM Fell English SC. Body text stays legible;
  flourish lives in headers and labels.
- **Cartography:** hand-drawn ink conventions — ridge-and-shadow peaks, hill
  arcs, pine glyphs, marsh tufts, wave lines — deterministically jittered so
  the map is sketchy but *stable across renders*. Uncharted hexes are blank
  parchment with dotted bounds.
- **Map marks:** black ink for the world (places, terrain), red ink for the
  player (position ring, route dashes, destination circle). Rumoured places at
  reduced opacity.

## 10. Interface behaviour

- The whole interface is: map + prose + free text input. Action buttons are
  *suggestions* — always clickable, never exhaustive; typing anything remains
  first-class. Suggested actions should be interesting, not optimal.
- Stat chips show only what the character would know about themselves
  (supplies, weariness, company, season) — ~4 max. No derived meters that leak
  simulation values.
- One map render per state change; the newest map is the live one.

## 11. Content altitude (for setting authoring)

Hand-author the top of the pyramid; generate the tail; let play canonize the
rest.

- **Hand-authored:** regions, subsystems (all five parts, carefully), major
  organisations, anchor characters, the style bible.
- **Generated with review:** places, minor organisations, T2-ready character
  stubs, rumour tables — checked for link integrity and schema compliance.
- **Emergent in play:** T1 individuals, scene detail, location canon — written
  down when they happen, per the engine's canon rules.

Design smell tests for setting content: every area inside 2+ footprints
(engine SS-4); every subsystem manifests at human scale in at least three
different ways; every anchor character wants something from another anchor.

## 12. Anti-goals

Deliberate refusals; each is load-bearing.

- **No exclusive biomes.** Subsystems overlap; terrain is where things happen,
  not what happens.
- **No visible machinery.** No subsystem names, meters, or footprints in any
  player-facing surface, ever.
- **No mechanics crunch.** We borrow The One Ring's *altitude*, not its dice.
  Rules exist to constrain the fiction, and stay backstage.
- **No WASD, no real-time.** Meaning per decision over decisions per minute.
- **No infinite procedural sprawl.** A deep small world beats a shallow big
  one; content grows by play and deliberate authoring, not by autogeneration
  for its own sake.
- **No multiplayer (for now).** Canon-conflict resolution between players is a
  different, harder game; revisit only after the single-player loop sings.
- **No power fantasy economy.** No XP/levels; capability changes come from the
  fiction (wounds, reputations, friendships, titles, scars).
