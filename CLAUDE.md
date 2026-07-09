# Twin — living-world game on the Claude game engine

A single-player, text-first hex-crawl journey game set in a living simulated
world. The world is a markdown knowledge base ("world brain"); Claude Code is
the runtime — simulator, adjudicator, storyteller, and narrator. Play sits at
the altitude of The One Ring RPG's travel phase. The name is the thesis: a
digital twin of the world in the player's head — the human holds the pen, the
engine keeps the books (D-014, DESIGN pillar 6). Every world is itself
twinned — a mundane layer and a plural, multicultural mythic layer — and the
player character is always one of the twinned, who perceives both (LR-1..7,
D-017). Mythic events reach everyone else rationalized (LR-4).

## Source of truth

This project is spec-governed. Read before acting; cite requirement IDs
(e.g. `SIM-4`, `UI-1`) when implementing, reviewing, or explaining changes.

| Document | Authority over |
| --- | --- |
| `docs/ENGINE-SPEC.md` | Engine behaviour, schemas, contracts (numbered reqs) |
| `docs/SUBSYSTEMS.md` | Subsystem mechanics companion + archetype library |
| `docs/DESIGN.md` | Player experience, aesthetics, anti-goals |
| `docs/DECISIONS.md` | Rationale history (ADRs, append-only) |
| `docs/PLAN.md` | Milestones, task status, backlog (living doc) |
| This file | Session-operational rules only |

Keep `docs/PLAN.md` current: update task statuses in the same commit as the
work; new work items go to its backlog with grounding IDs, not into chat.

**Docs-first (CC-1):** if requested work contradicts the spec or design,
surface the conflict — then either follow the doc or amend it (version bump +
DECISIONS.md entry) before implementing. Never silently deviate. Requirement
IDs are never renumbered (withdraw in place, append new).

## Non-negotiable session rules

1. **Golden-path UI (UI-1..3).** Widgets are never improvised. Render
   `ui/travel-map.html` by replacing the exact token `/*__STATE__*/null` with
   a STATE JSON object (schema: UI-4) — byte-identical otherwise, widget title
   `travel_phase_map`. Template edits happen only in the file, only when the
   user asks.
2. **Subsystems are never shown (KN-3, UI-5).** No subsystem names, states, or
   footprints on any player-facing surface; manifestations only. Do not
   reintroduce map overlays.
3. **Player knowledge only (KN-2).** Nothing the character hasn't perceived,
   learned, or inferred reaches prose, map, stats, or actions.
4. **Events before summaries (EV-1..2).** Consequential happenings land in the
   append-only event log; entity files are views over events. Never silently
   rewrite canon (EV-4).
5. **Hard state in YAML (WB-4, AD-1).** Numbers and inventories are structured
   values updated deterministically; prose describes, YAML measures.
6. **No entity deletion during play (WB-5, SIM-3).** Retirement is a status
   change. People the player has met stay real.

## Repository layout

```
docs/     specs (source of truth)
ui/       golden-path widget templates (human-owned; see ui/README.md)
world/    dev fixture world brain only (layout: WB-1) — this repo is the
          engine and ships no setting (AR-4); real worlds ("twins") are
          separate repos that instantiate the engine
.claude/  skills and agent role prompts (engine runtime; planned: RT-2..3)
```

## Session boot (RT-1)

Sessions are amnesiac by design (AR-3): re-enter the world through files.
For play sessions: read `world/state/` (clock, player, map) and active
threads before the first beat. For engineering sessions: read the relevant
spec sections before changing anything.

## Git

Work on `main` is fine while the project is single-contributor. Conventional
commits (`feat:`, `fix:`, `docs:`, `chore:`). Spec changes and their
implementation should land together, spec first in the diff.
