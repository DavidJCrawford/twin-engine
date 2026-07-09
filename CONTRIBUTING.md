# Contributing to Twin

Thanks for your interest. Twin is unusual: the runtime is an LLM agent
reading the repository, which means **the specifications are not
documentation about the engine — they largely are the engine.** Precision in
the spec becomes runtime behaviour. Contributions are judged accordingly.

## The one rule that governs everything

This project is spec-governed (see `CLAUDE.md`). Before changing anything,
read the relevant sections of `docs/ENGINE-SPEC.md`, `docs/DESIGN.md`, and
`docs/SUBSYSTEMS.md`. If your change contradicts them, change the docs first
(or in the same PR, docs first in the diff) — never deviate silently.

## Ground rules

1. **Cite requirement IDs.** Changes reference the requirements they
   implement or amend ("per SIM-4", "amends UI-4"). If no requirement
   covers your change, that's a spec gap — propose the requirement.
2. **Spec changes** bump the document's version (CC-3: patch/minor/major)
   and add a rationale entry to `docs/DECISIONS.md`. Requirement IDs are
   never renumbered or reused; withdrawn requirements are marked in place.
3. **DECISIONS.md is append-only.** Reversing a recorded decision takes a
   superseding entry, not an edit.
4. **Keep the plan honest.** Update task statuses in `docs/PLAN.md` in the
   same commit as the work.
5. **Conventional commits**: `feat:`, `fix:`, `docs:`, `chore:` with scope
   where useful (`feat(ui): ...`, `feat(engine): ...`).

## Area-specific notes

- **UI / map art** — golden-path rules apply (UI-1..UI-3): all visual work
  happens in the template files, never improvised per render. Iterate in
  `ui/terrain-lab.html` (open it in a browser — it's self-contained).
  All generated art must be **deterministic**: seeded per hex/edge, never
  `Math.random()`, so identical state renders identical maps.
- **Subsystem archetypes** — new catalog entries must pass the SS-14
  authoring checklist and use culture-neutral naming with peer variant
  forms (AR-4). If your archetype only makes sense in one culture's terms,
  generalize it or it belongs in a setting, not the engine.
- **Setting content** — none lands here, ever (AR-4). Worlds are separate
  repositories; see `docs/TWIN-GUIDE.md`. Fixture data (the Barrowford demo)
  may be improved but is disposable placeholder by definition.

## AI-assisted contributions

This project is built *with* Claude Code as a first-class tool — the repo's
`CLAUDE.md` is the session constitution. AI-assisted PRs are welcome on the
same terms as any other: the contributor is responsible for the content, the
governance rules above apply, and "the model wrote it" is neither a
disqualifier nor an excuse.

## Conduct

Be kind, be direct, assume good faith. Worldbuilding touches culture and
history; when a discussion involves living cultures' material, defer to the
people it belongs to. Maintainers may close anything that makes the project
worse to be around.

## Licensing

Contributions are accepted under the MIT license (inbound = outbound).
Worlds built with the engine belong to their makers.
