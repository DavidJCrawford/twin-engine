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

- hard state: coin 3 shillings 4 pence (40d; fixture money: 12 pence = 1
  shilling, written 3s 4d) · food 2 days · weariness rested
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

1. "The wolves are bold" — heat HOT (Wren crosses into wolf country at dawn;
   dark of the moon tomorrow night — Berrin wants the flock over the water
   before it)
2. "Twenty shillings by the fair" — heat hot (path to 12s secured; 8s gap
   remains; premium possible)
3. "The lake is hungry again" — heat cold, building (appeasement fading)

## Agreements / obligations

- (ACTIVE, struck midday 1 Frostfall) The drove: Wren + Berrin bring ~60
  wethers over the ford from Hale's west grazing (dawn, 2 Frostfall), then
  drove to the fair at Nen's Rest by 9 Frostfall. Pay 12s: 6 at the ford,
  6 at delivery. Stretch term: the buyer pays a premium (~2s) for the
  first fat pens sold — penned by evening of the 8th.
- (standing) 20s owed to Aldous, due at the fair (day 10). Gap after drove
  pay: 8s (6s if premium made).
- (OFFERED by Aldous, afternoon day 1 — mutually exclusive, unaccepted):
  a) rollover: pay 12s at the fair; remaining 8s carries to Thaw at
     interest — "eight becomes ten by Thaw";
  b) the parcel: carry a small oilcloth-sewn packet to Corby the
     eel-seller at the Frostfall fair, unopened — 8s forgiven entire.
     (Parcel is smuggling-network cargo; contents undisclosed; heat
     transfers to bearer if caught. Wren not told this in terms.)

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
- 1 Frostfall, midday: bargain struck on Berrin's terms (see Agreements).
  Impact: grain-and-herds +minor to ledger (drove crewed — accrues, does
  not move state, AD-5). Berrin names the dark of the moon tomorrow night
  as his reason for haste. Rest of day 1 free to Wren
  — visibility: witnessed (Wren, Berrin); rumour-network picks up "Wren
  drives with Berrin at dawn" (true, local)
- 1 Frostfall, afternoon: Wren calls on Aldous. Aldous ALREADY KNEW of the
  drove (rumour network, village-internal, same-day — "news walks faster
  than sheep"). Terms offered (see Agreements): rollover at interest, or
  the parcel to Corby. Parcel: small, sewn in oilcloth, heavy for its
  size. Aldous names the marsh men "at the fair in number this year"
  — visibility: witnessed (Wren, Aldous)

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
- Agreements need a **status field** (pending/active/fulfilled/broken) and
  optional **stretch terms** — confirmed the schema note from beat one.
- Scene-choices widget needed the map's context header (location, date,
  weather, chips) — without it the player loses bearings between map
  renders. RESOLVED: UI-8 schema v1.1 (spec 0.12.0). Play-driven UI
  refinement #2.
- "Coin: thin" was a design error caught by the owner: material survival
  must be EXACT (coin in denominations, food in days) — grounding principle
  now in DESIGN §10 (0.5.0). Wren's purse fixed at 3s 4d, food 2 days.
  Season chip dropped as redundant with the date line. Refinement #3 —
  and a validation of WB-4: the ledger already measured; only the display
  was vague.
- Per-NPC knowledge tracking is unaffordable and mostly unnecessary:
  adopted a default — events propagate to NPCs by visibility + locality
  (village-internal public/local events are known same day) and only get
  per-NPC exceptions when it matters. M1's knowledge model should encode
  this default rather than demand per-NPC files (KN-1 scope note).
- Refinement #4: "3s 4d" confused the owner — notation shipped before the
  world taught it. DESIGN rule added (0.5.1): denominations are setting
  data, taught diegetically before abbreviated. Chip labels also gained a
  colon separator (both templates) — label and value were running together.
- Refinement #5: narration belongs INSIDE the widget, above the choices —
  the scene widget is the journal page, complete: header, prose, decision.
  UI-8 schema v1.2 adds `prose: [paragraphs]` (spec 0.13.0). Consequence:
  chat text shrinks toward zero during beats; the widget is the game
  window. This is quietly the M7 client's layout discovering itself.
- Weariness (hard state) becomes decision-relevant the moment a dawn start
  exists: the "rest early" option has mechanical meaning (AD-1). Player
  stat chips are earning their place.
- UI-6 friction: as written it implied a map re-render every scene beat
  (actions always change). RESOLVED mid-session: UI-6 amended + UI-8
  scene-choices widget added (spec 0.11.0) — first UI evolution driven by
  paper play. Also fixed: map action buttons unreadable (white text) in the
  host's dark theme — host button styles overrode template colors; both
  templates now force colors.
