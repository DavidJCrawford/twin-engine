# Twin

A living-world journey game, and an experiment in what agentic AI can do for
games — built on the conviction that the answer should put humans more in
charge of their stories, not less.

## The idea

Imagine the notebook a great Dungeon Master keeps: places, people, factions,
grudges, weather, rumours, debts — except a thousand times more detailed, and
it writes itself. Twin stores an entire world as a structured knowledge base
(the "world brain"): thousands of characters, places, and organisations, and
hundreds of overlapping subsystems — ecologies, economies, faiths, feuds —
each with its own state and dynamics, sharing and contesting the same
geography.

An AI agent (Claude Code today, the Claude Agent SDK later) is the game
engine. It ticks the subsystems forward whether or not anyone is watching,
adjudicates whatever the player attempts, keeps every promise and consequence
on the books, and narrates. The player inhabits one ordinary-scale person in
this world and travels it as a hex crawl, at the decision altitude of a great
tabletop campaign's travel phase: choose a road, a camp, a companion, a
promise — then live with what follows. Come back to a town a year later and
it is a year later: the famine deepened or broke, the innkeeper's daughter
left for the city, and the merchant you ruined has been telling stories about
you.

None of this runs on a traditional game engine. The world is markdown; the
simulation is an agent reading and writing it under a strict specification.
That makes the world radically flexible (anything describable is simulable)
while staying consistent (canon is written down, provenance is kept, and the
world is forbidden from gaslighting you about anything you've seen).

## One world, twice

Every Twin world is layered. Beneath the material, historical world lies a
mythic one: the beings, spirits, and old bargains of every culture that
lives there or ever arrived. Traditions are plural and they travel — the
peoples of the land have their ancient, rooted powers, and migrants bring
their own beings with them in the holds of their ships, thin and hungry and
far from the soil that fed them. The two ecologies collide, bargain, and
adapt, exactly as the mundane ones do.

Most inhabitants see only one world. The player is always one of the
*twinned* — a person who lives in both, seeing truly what everyone else
rationalizes away as weather, accident, or luck. Where the village hears
that a swimmer drowned, you saw what pulled him down.

This is the engine's one fixed creative constraint, and it is deliberately
structural rather than prescriptive: no culture, era, tone, or content is
imposed. It exists because good constraints are generative. Any place and
any pre-industrial time can answer the questions it asks — *whose land is
this, who came, what did they bring, and who can see?* — and almost every
answer is a world worth building.

## Why "Twin"

The project is named for what it is meant to become: a **digital twin of the
world in your head**.

Anyone who has run a tabletop campaign, written a novel, or daydreamed a
setting on a long commute knows the problem: the world in your imagination is
vivid and enormous, and the bookkeeping needed to make it *real* — consistent
histories, hundreds of named people who stay themselves, economies that react,
time that actually passes — is beyond any one person's patience. That
bookkeeping is exactly what an agentic knowledge base is good at.

So Twin splits the work the way a great gaming table splits it:

| The human | The engine |
| --- | --- |
| Imagines the world | Keeps the books |
| Decides what matters | Simulates what follows |
| Drives the story at its turning points | Plays everyone and everything else |
| Plays the character | Remembers everything, forever |

Today the game ships with its own setting. The goal for public release is a
**worldbuilding layer**: tools that let you build out the concept in your own
mind — your places, your people, your tensions — and then step into it, steer
the story where it matters to you, and leave the dice-rolling and
record-keeping to the machine.

## The motivation

This project exists to explore a question: can agentic tools and agentic
knowledge bases create genuinely new kinds of play — experiences that push
the envelope of what a "game" can be?

And it takes a deliberate stance on how to answer it. The easy use of
generative AI in games is to let the machine improvise everything, and the
result is mush: worlds with no memory, stories with no author, choices with
no weight. Twin is built the opposite way around. The AI here is
infrastructure — a tireless simulationist and lore-keeper operating under
rules a human wrote — and every layer of the design (the specs in `docs/`
are explicit about this) exists to protect human authorship: the player's
deeds are the strongest force in the world, the worldbuilder's canon is
inviolable, and the machine's job is to make their imagination hold together
at a scale imagination alone can't manage.

If Twin works, it won't feel like talking to an AI. It will feel like the
world you made up is, at last, actually there.

## How it works (the short version)

- **World brain** — all state is a markdown vault: typed entities, stable
  IDs, an append-only event log as the spine, and hard separation between
  world-truth and what any character knows.
- **Subsystems, not biomes** — the simulation unit is a system (a wolf
  population, a smuggling network, a heresy) with state, dynamics, a
  geographic footprint, couplings to other subsystems, and rules for how it
  manifests at human scale. Footprints overlap; the interactions are where
  the stories come from.
- **Foveated simulation** — the world advances in aggregate everywhere;
  individual people are simulated in detail only near the player or on hot
  story threads. Anyone the player meets becomes permanent canon; anyone the
  player *matters to* gets a life of their own that advances off-screen.
- **Prose pipeline** — a small crew of specialised agent roles (director,
  writer, editor) with durable style canon, because in a text game the prose
  is the graphics engine.
- **Golden-path UI** — the hex-map interface is a human-owned template the
  engine fills with state, so the presentation is exactly as consistent as
  its author wants it to be.

The full engineering treatment lives in [`docs/ENGINE-SPEC.md`](docs/ENGINE-SPEC.md)
(the engine contract, numbered requirements), [`docs/DESIGN.md`](docs/DESIGN.md)
(the experience: pillars, loop, voice, anti-goals),
[`docs/DECISIONS.md`](docs/DECISIONS.md) (why each choice was made), and
[`docs/PLAN.md`](docs/PLAN.md) (milestones and backlog).

## Status

Early and deliberately spec-first: the concept, engine specification, design,
and map UI exist; the first world and the playable loop are next (see
[`docs/PLAN.md`](docs/PLAN.md)). The prototype runs entirely inside Claude
Code — the repository *is* the game.

## Repository

```
docs/     specifications — the source of truth for everything here
ui/       golden-path widget templates (human-owned)
world/    the world brain (setting content; arrives with milestone M1–M2)
.claude/  engine skills and agent role prompts (arrives with M3)
CLAUDE.md session constitution for the AI runtime
```
