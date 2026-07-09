# Building a twin — instantiation guide

**Version:** 0.2.0 (2026-07-10)
**Audience:** anyone creating a world with the Twin engine, including the
engine's own author (AR-4: every world walks through this same door).

A **twin** is a world built on this engine: its own repository, its own
canon, its own aesthetic — running on the engine's specs, schemas, skills,
and UI contracts.

## 1. Create the repository

The engine repo is a GitHub template.

- On GitHub: **Use this template → Create a new repository**, or:
- CLI: `gh repo create <you>/<twin-name> --template DavidJCrawford/twin-engine --private --clone`

Start private if you like; go public when ready. Then wire up the upgrade
path immediately (you'll thank yourself later):

```sh
git remote add engine https://github.com/DavidJCrawford/twin-engine.git
git fetch engine --tags
```

## 2. What you own vs what stays engine-governed

| Yours to change freely | Engine-governed (upgrade, don't fork) |
| --- | --- |
| `world/` — everything (this IS your twin) | `docs/ENGINE-SPEC.md` |
| `docs/SETTING.md` — your world bible (create it) | `docs/SUBSYSTEMS.md` (the archetype library) |
| `CLAUDE.md` — extend with setting rules; keep the engine rules | `docs/DESIGN.md` (extend via SETTING.md, don't rewrite) |
| `ui/` skinning per UI-7: glyphs, terrain art/palette, type, chrome language, dual naming | UI injection contract + STATE schema (UI-1..4) |
| `README.md`, `docs/PLAN.md` — repurpose for your project | `docs/TWIN-GUIDE.md`, `CONTRIBUTING.md`, `LICENSE` |
| The Barrowford fixture data — replace it | `docs/DECISIONS.md` — keep the engine's entries, append your own |

If you need an engine change (new STATE field, new archetype, a spec
amendment), propose it upstream (CC-1) rather than forking the governed
docs — that keeps your twin upgradeable and improves the engine for everyone.

## 3. First-session checklist

Work through these in your twin's first sessions (they map to engine
requirements):

1. **World identity** — `docs/SETTING.md`: place, era, tone, what the game
   is about. Record your engine baseline: "built on twin-engine vX.Y.Z /
   ENGINE-SPEC a.b.c".
2. **Calendar** (TM-5) — seasons, reckoning, festivals, any day-quality
   system. This is load-bearing: the season clock reads it.
3. **The layers** (LR-1..5) — name your traditions (rooted and migrant),
   decide the mythic layer's density and tone, and name and price the gift
   of the twinned in your world's terms.
4. **Cultural care** — if your twin draws on living cultures' history,
   beliefs, or beings, write a cultural-care policy as a governing document
   of your repo *before* authoring content: what you draw from published
   sources, what you invent in the spirit of, what you leave alone, who (if
   anyone) reviews it. Living heritage deserves engineering-grade care.
5. **Starter subsystems** — instantiate from `docs/SUBSYSTEMS.md` (§5 shows
   the interlocking-starter pattern: two cores + ~5 setting systems, every
   pair coupled, every area in 2+ footprints).
6. **Geography** — your first region's hex grid, rivers, roads, coasts
   (UI-4); re-skin the map template (UI-7) to your artifact conceit.
7. **Souls** — anchor characters (T2 detail), stubs for the rest, your
   twinned player character (LR-5) and their starting knowledge file.

## 4. Upgrading the engine

Engine releases are tagged (`v0.2.0`, ...). To upgrade a twin:

```sh
git fetch engine --tags
git merge v0.2.0 --allow-unrelated-histories   # first upgrade only
git merge v0.3.0                               # subsequent upgrades
```

Conflicts should land almost entirely in files you own (fixture data, README,
CLAUDE.md extensions) — resolve in your favor. Conflicts in engine-governed
docs mean you forked them; reconcile toward upstream. After upgrading, update
your recorded engine baseline in SETTING.md and skim the new DECISIONS
entries — they tell you what changed and why.

## 5. Contributing back

Generic improvements discovered while building your twin — new archetypes,
tile art, spec gaps, terrain types — belong upstream; see `CONTRIBUTING.md`.
Your world stays yours (MIT: the engine never claims your content).

## 6. Publishing your world for players (OB-8..OB-10)

A finished twin can be a playable world others step into — the same
template mechanism the engine uses, one level up. Players who instantiate
your world land directly in character creation (`/begin` routes by repo
state) and their repo becomes their save: a divergent timeline of your
world at the release they took, theirs forever. You keep authoring your
canon; their playthroughs never touch it.

**Publishing checklist** (spec OB-8):
1. Minimum viable world complete (OB-4) and every subsystem passing SS-14.
2. Care policy finalized, where §3.4 requires one.
3. A player-facing README: what the world is, its register and tone, and
   honest content notes (folk horror is dark; say so).
4. Spoiler layout (OB-10): world-truth mysteries under `world/secrets/`,
   clearly signposted — players honor it like a gamemaster's screen.
5. Tag a world release: `git tag world-v1 && git push origin world-v1`.
6. Make the repo public and mark it a template (repo settings, or
   `gh api -X PATCH repos/<you>/<world> -F is_template=true`).

**For your players**, include instructions like:

```sh
gh repo create <them>/my-<world>-playthrough --template <you>/<world> --private --clone
# open in Claude Code, run /begin — it will take you to character creation
```

**Updates:** players pin the release they started from; their timeline has
diverged, so world updates (`world-v2`) are an offer, not an upgrade path.
Engine upgrades (§4) remain available to them independently.
