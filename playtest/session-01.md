# Paper playtest — session 01 (Barrowford fixture)

Hand-cranked run of the spec before M1 exists. Everything here is what the
engine will one day keep in `world/` — tracked manually to learn what the
schemas must hold. Fixture content only (AR-4); throwaway canon.

## Clock

- date: 1 Frostfall 1211 (day 1)
- deadline: the Frostfall fair at Nen's Rest, 10 Frostfall (debt due)

## Subsystems (paper instances, SS-5 ordinal states)

- season-clock (world): season Frostfall · severity moderate, drift rising
- rumour-network (world): active claims —
  - "wolves took a drove dog west of the ford" (true)
  - "Greywatch Keep stands empty since autumn" (FALSE — wardens present, stretched)
  - "the fair at Nen's Rest will be thin this year" (true-ish)
- wolf-pressure (regional): pressure high · packs 4 · fear-of-man low
- grain-and-herds (regional): stores moderate-low · last drove of season pending
- marsh-smuggling (regional): volume moderate · discipline moderate · heat low
- greywatch-wardens (regional): patrol-strength low · legitimacy moderate
- nen-of-the-lake (mythic; tradition: the Old Rites, rooted; layer: mythic):
  appeasement fading · hunger low, drift rising · footprint: lake + Nen's
  Rest + Drowned Chapel · manifests: drownings ("cramp"), fish gone from
  the shallows, cold that comes off the water wrong · signature: drowned
  things surface on the third day, faces calm · audience: the sighted see
  the wake with nothing above it
- player-renown (regional): unknown

## Player character (proposed pregen — depth dial: delegate, revisable)

- Wren, a drover — knows the west road and the fair circuit
- twinned: the sight, since age nine — saw what took a swimmer at the lake
  while the village called it cramp; thought odd ever since
- people: Berrin (old drover, mentor, T2) · Maren (keeps the Ford Inn, T2)
- obligation: 20 shillings owed to Aldous the wool-buyer, due at the fair
  (Aldous quietly moves smugglers' coin — hook into marsh-smuggling)
- knowledge: starts with rumour list above (believes the Greywatch one —
  it is false)
- authorship: generated (OB-3 — player may adopt, edit, or reroll)

## Threads

1. "The wolves are bold" — heat warm (wolf-pressure high + drove pending)
2. "Twenty shillings by the fair" — heat hot (deadline day 10)
3. "The lake is hungry again" — heat cold, building (appeasement fading)

## Agreements / obligations

- (pending) Berrin's offer: bring the flock over the ford from the west
  grazing (dawn, 2 Frostfall), then drove to the fair at Nen's Rest by
  9 Frostfall. Pay 12s: 6 at the ford, 6 at delivery.

## Event log

- (setup) 1 Frostfall: hard frost; geese over low, silent, three days early
  — visibility: public; the silence noticed only by the sighted
- 1 Frostfall, morning: Wren finds Berrin at the yards. Learned: it was
  Berrin's young dog Jess taken two nights back, west of the ford; kill was
  eaten clean (wolf signature, mundane). Flock of ~60 wethers still on the
  west grazing; Berrin offers the season's last drove (terms above). Berrin
  counters the Greywatch rumour: saw chimney smoke from the high field —
  keep manned, but thin. Wren's knowledge now holds BOTH claims, unresolved
  — visibility: witnessed (Wren, Berrin)

## Meta-observations for M1 (append during play)

- Needed immediately: per-claim truth flags on rumours; a deadline field on
  threads; audience tags on manifestation notes; "drift" alongside ordinal
  values. All confirm SS-5/ND-3/SS-9 shapes.
- Needed an **agreements** structure (parties, terms, staged payment,
  deadline) — not cleanly a thread or a ledger entry. M1 should give
  obligations first-class shape (extends OB-6's "obligations as hooks").
- Knowledge file must hold **conflicting claims side by side** (empty-keep
  rumour vs Berrin's counter-evidence) with sources — KN-5 + EV-4 pattern
  applies to knowledge, not just canon. Confirmed by play in beat one.
- UI-6 friction: as written it implied a map re-render every scene beat
  (actions always change). RESOLVED mid-session: UI-6 amended + UI-8
  scene-choices widget added (spec 0.11.0) — first UI evolution driven by
  paper play. Also fixed: map action buttons unreadable (white text) in the
  host's dark theme — host button styles overrode template colors; both
  templates now force colors.
