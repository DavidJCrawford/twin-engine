# Subsystem library and mechanics guide

**Version:** 0.2.0 (2026-07-10)
**Status:** Engine-layer document. Implements and extends `ENGINE-SPEC.md` §6
(SS-1..SS-14). Archetypes here are setting-agnostic templates (AR-2); a world
instantiates them with names, footprints, and tuned variables. This is also
the template library the future worldbuilding layer exposes to players
(D-014).

---

## 1. How subsystems work (mechanics summary)

A subsystem is five parts (SS-1): **state**, **dynamics**, **footprint**,
**couplings**, **manifestation layer**. The mechanics that make those parts
simulate well:

### State (SS-5)
Variables are typed; prose never carries a number the YAML doesn't.

| Type | Form | Example |
| --- | --- | --- |
| `level` | 5-point ordinal: `dormant, low, moderate, high, critical` | wolf pressure: high |
| `stock` | number + unit | grain: 340 sacks |
| `flag` | boolean | bridge_out: true |
| `clock` | fires on an in-world date | dam_breaks: 12 Thaw unless repaired |
| `extent` | list of areas/hexes | blight_reach: [E3, F3, F4] |

Ordinal levels are the default; use `stock` only where the exact quantity is
playable (supplies, coin, troops). Every subsystem SHOULD have 2–5 variables.
One variable, no system; eight variables, no legibility.

### Dynamics (SS-6)
Plain-language rules the tick executes, in three groups:
- **Baseline drift** — what happens with *no* inputs (recovery, decay,
  seasonal swing). Every subsystem MUST define drift; a system that only
  moves when pushed is a prop, not a system.
- **Conditional rules** — `if <state/coupling condition> then <change>`.
- **Threshold events** — level transitions that emit events (EV-1) and
  manifestation changes ("pressure reaches critical → packs enter villages").

### Couplings (SS-7) and damping (SS-8)
A coupling is a tuple: `{from: subsystem.variable, to: variable, sense: +/−,
strength: weak|strong, latency: ≥1 tick, condition?}`. The rules that keep
the web stable:
- **Latency is never zero.** Influence takes at least one tick to arrive.
- **Depth-1 propagation.** A tick applies direct couplings only; knock-on
  effects happen next tick. Cascades are possible but *slow*, which is both
  stable and dramatically legible (the player can see trouble coming).
- **One step per tick.** A `level` moves at most one step per tick, however
  many pushes it gets — unless a `structural` impact (AD-4) forces more.
  Slow systems are believable systems.
- **Conflicts are content.** When two subsystems push the same variable
  opposite ways, the tick holds it steady and flags the tension to the
  storyteller (ND-1). Deadlock between systems is a story seed, not an error.

### Manifestations (SS-9)
Keyed by variable level, spread across six channels:
`encounter` (journey beats), `rumour` (with a truth flag — rumours may lie),
`price` (goods/costs), `sensory` (scene dressing), `npc` (mood/behaviour
shifts), `travel` (speed, danger, route viability).

- **Fairness rule:** every variable that can affect the player MUST have at
  least one perceivable manifestation before it bites. No invisible gotchas.
- **Signature:** each subsystem defines how a perceptive observer tells its
  fingerprints from a neighbour's (wolf-kills are eaten; something darker
  wastes the carcass). Signatures are what make world-reading a learnable
  skill (DESIGN §5).

### Scale, cadence, lifecycle (SS-10, SS-11)
- `scale: world | regional | local` — world ticks seasonally, regional
  weekly-to-monthly, local weekly. Ticks process world → regional → local,
  so big weather arrives before the village reacts to it.
- `status: nascent | active | dormant | resolved` — subsystems are born
  (often spawned from a template by the storyteller: a plague arrives),
  sleep, and end. Resolved subsystems keep their file (WB-5) as history.

### Ledger and index (SS-12, SS-13)
- Sub-`notable` impacts accrue in the subsystem's own `ledger:` list and may
  tip a variable at tick time (AD-5's mechanism).
- `world/state/footprints.md` maps hex → subsystem ids, rebuilt whenever a
  footprint changes, so travel planning and manifestation sampling are one
  lookup, not a vault scan.

## 2. Worked example

```markdown
---
type: subsystem
id: ss-wolf-pressure-westmarch
name: Wolf pressure (Westmarch)
archetype: predator-population
scale: regional
status: active
footprint:
  areas: ["[[Westmarch]]"]
variables:
  pressure:    {type: level, value: high,  note: "how boldly packs hunt near men"}
  pack_count:  {type: stock, value: 4, unit: packs}
  fear_of_man: {type: level, value: low}
ledger: []
couplings:
  - {from: "[[ss-season-clock]].severity", to: pressure, sense: "+", strength: strong, latency: 1}
  - {from: pressure, to: "[[ss-drover-economy]].route_safety", sense: "-", strength: strong, latency: 1}
  - {from: "[[ss-greywatch-wardens]].patrol_strength", to: pressure, sense: "-", strength: weak, latency: 2}
---

## Dynamics
- Drift: pressure moves one step toward `moderate` in green seasons, toward
  `high` in Frostfall and winter.
- If pressure ≥ high for 3 consecutive ticks: emit "livestock kills" events
  in bordering settled hexes.
- If pressure = critical: packs enter village hexes at night (encounter hook,
  visibility: local).
- Hunting parties (impact ≥ notable) reduce pack_count; pressure follows
  pack_count down with 1-tick lag; fear_of_man rises one step.

## Manifestations
- high / sensory: carcasses by the road; ravens; tracks over tracks.
- high / rumour (true): drovers refuse the west road unless paid double.
- high / price: mutton scarce, wool up a third.
- high / encounter: a pack shadows travellers at dusk; bolder against fewer
  than four.
- critical / npc: shepherds sleep in the folds; children kept indoors; talk
  of hiring hunters.

## Signature
Wolf-work is eaten, not wasted; tracks shy from iron and fire. Old hands can
tell it from something darker.
```

## 3. Archetype catalog

Format: **Archetype** (scale) — key variables · typical couplings · shows as.
Entries marked ⚝ suit settings with the supernatural; skip or reskin freely.

Archetype names are deliberately culture-neutral (AR-4). Where an entry lists
variant forms, they are *peer* forms — a chiefly title system is not an
exotic flavor of feudal lordship, nor vice versa. If instantiating an
archetype for a setting requires bending it past recognition, the archetype
is wrong: generalize it here first.

### 3.1 Wilds and beasts
- **Predator population** (regional) — pressure, pack count, fear of man ·
  winter +, patrols −, herds − · carcasses, wary drovers, dusk encounters.
- **Game population** (regional) — abundance, wariness · predators −,
  hunting − · full/empty larders, poaching disputes, cheap or dear hides.
- **Migratory passage** (regional, seasonal) — timing, volume · season
  clock + · skies full of wings, hunters' camps, festival timing.
- **Monster territory** ⚝ (local) — activity, range, hunger · game −,
  ruins + · claw-marked trees, missing livestock, a bounty posted.
- **Vermin swarm** (local) — infestation · sanitation −, granaries − ·
  gnawed sacks, cats prized, ratcatchers hired.
- **Invasive species** (regional) — spread extent, native decline · trade
  routes + (carried in) · strange weeds, old crops failing, new remedies.
- **Fishery** (local) — stocks, fleet size · river flow +, overfishing − ·
  catch size, market smell, idle boats.
- **Pollinators** (regional) — hive health · blight −, weather ± · honey
  prices, thin orchards, beekeeper worry.
- **Forest health** (regional) — canopy, regrowth, deadwood · timber
  industry −, wildfire − · logging camps, young growth, char smell.

### 3.2 Land and weather
- **Season clock** (world; near-mandatory core) — season, severity ·
  couples into almost everything · daylight, festivals, road states. Other
  subsystems read it; it reads nothing.
- **Storm regime** (regional, seasonal) — frequency, ferocity · season + ·
  wrecked roofs, delayed boats, omens read in thunder.
- **Flood cycle** (regional) — river height, levee state · storms +,
  snowmelt + · drowned fields, ferry-only crossings, silt-rich planting.
- **Drought / water table** (regional) — reserves, crop stress · season ±,
  irrigation + · low wells, dusty roads, rain prayers.
- **Wildfire regime** (regional, seasonal) — fuel load, burn risk · drought
  +, forest health − · haze, fire watches, blackened tracts.
- **Avalanche / slide risk** (local, mountain) — snowpack, stability ·
  storms +, thaw + · closed passes, roped guides, buried waymarks.
- **Soil fertility** (regional, slow) — exhaustion, fallow discipline ·
  harvest pressure − · shrinking yields, field disputes, marling crews.
- **Endemic miasma** (local, marsh) — virulence (disease reservoir) ·
  season ±, drainage − · fevers, amulets, guides who won't camp low.
- **Tides and currents** (regional, coastal) — spring/neap, hazards ·
  moon/season · sailing windows, exposed flats, wreck salvage.

### 3.3 Fields and food
- **Harvest cycle** (regional; core for settled areas) — stores, growing
  conditions, yield outlook · weather ±, labor +, blight − · bread prices,
  granary guards, gleaners in the fields.
- **Crop blight** (regional, spawnable) — spread extent, virulence · trade
  + (carried), fallow − · spotted leaves, burned fields, quack cures.
- **Livestock economy** (regional) — herd size, condition, drove schedule ·
  predators −, murrain −, markets + · droves on the road, shearing season,
  meat prices.
- **Murrain / livestock disease** (regional, spawnable) — spread, mortality
  · droves + (carried), quarantine − · culled herds, closed fairs, ruined
  drovers.
- **Food distribution** (regional) — flow between surplus and want ·
  harvest +, roads +, tolls −, war − · grain carts, hoarding accusations,
  relief trains. Famine is this system failing, not a system of its own.
- **Hunting and foraging** (local) — take, rights enforcement · game +,
  lordship − · snares, wardens, poaching trials.

### 3.4 Trade and coin
- **Trade route** (regional; one per major artery) — traffic, safety,
  toll burden · banditry −, road state +, wars − · caravans, inn business,
  goods variety at market.
- **Market cycle** (local) — fair calendar, attendance · routes +, season ·
  crowded squares, price swings, strangers in town.
- **Mining operation** (local) — vein richness, workforce, safety · luxury
  demand +, flooding − · ore trains, widow processions, boomtown swagger.
- **Timber industry** (local) — cut rate, camps · forest health −, building
  demand + · rafts on the river, stump fields, sawdust wages.
- **Craft guild economy** (local, urban) — output, quality control,
  apprentice supply · materials +, fashion + · workshop rows, masterpiece
  examinations, guild marks.
- **Moneylending / debt web** (regional) — credit tightness, default rate ·
  harvest −, war + · foreclosures, dowry loans, a lender's ledger of names.
- **Currency health** (world, slow) — debasement, trust · realm treasury −
  · clipped coins, barter revival, assay scales at market.
- **Luxury demand** (world, slow) — fashion for a good (silver, fur, dye) ·
  court culture + · price booms, prospector rushes, counterfeits.
- **Labor supply** (regional, seasonal) — hands available, wage pressure ·
  harvest peaks +, war levies −, plague − · hiring fairs, wage disputes,
  fields ripe and unmowed.
- **Banditry** (regional) — band count, boldness, haunts · war veterans +,
  trade wealth +, patrols − · burned wagons, toll "offers", wanted boards.
- **Piracy / wrecking** (regional, coastal) — activity, havens · naval
  patrols −, trade + · lost ships, false lights, hanged men on the quay.

### 3.5 Powers and wars
- **Authority and law** (regional; core) — law strength, legitimacy,
  reach-by-area · unrest −, coin + · patrols, disputes settled or not,
  whether anyone answers a call for help. Forms: feudal lordship, chiefly
  prestige-authority, elder council, magistracy, occupation regime — a
  region may hold several at once, contesting reach.
- **Garrison and logistics** (regional) — strength, supply, morale ·
  treasury +, harvest + · requisitions, drills, deserters in the hills.
- **Border tension** (regional) — hostility level, incident count ·
  diplomacy −, incidents + · patrol clashes, refugee trickles, merchants
  rerouting.
- **War front** (regional, spawnable) — front line extent, intensity,
  supply · escalates from border tension · levies marched off, foraging
  parties, the sound of distant guns/horns.
- **Rebellion / unrest** (regional) — grievance, organization, boldness ·
  taxes +, famine +, repression ± · seditious talk, night meetings, a
  gibbet as warning.
- **Succession question** (world/regional, spawnable) — claimant strength,
  faction alignment · deaths, marriages, oaths · court intrigue, oaths
  tested, private wars.
- **Feud** (local/regional) — heat, body count, honor stakes · insults +,
  mediation − · averted eyes at market, ambushes, wedding-as-treaty.
- **Diplomatic web** (world, slow) — alignments between realms ·
  everything · embassies, tariffs, marriage brokering.
- **Espionage network** (regional) — coverage, exposure risk · wars +,
  courts + · too-curious pedlars, coded letters, a hanged "merchant".
- **Mercenary market** (regional) — free-company supply, employment ·
  wars ± (peace floods it) · companies in camp, recruiting sergeants,
  paid-off trouble looking for work.
- **Justice and redress** (regional) — process availability, severity,
  corruption · authority +, coin − · judgments handed down, outlaws made,
  appeals to the road. Forms: circuit courts and assizes, elder arbitration,
  reciprocal-satisfaction customs (compensatory raids, honor payments),
  trial by ordeal.
- **Tax regime** (regional) — burden, collection vigor · treasury need +,
  unrest − · collectors with escort, hidden herds, arrears lists.

### 3.6 Faith and festival
- **Established observance** (regional; core where settled) — observance,
  officiant strength, offering flow · festivals +, rival observances −,
  disasters ± (piety spikes) · full/empty shrines, processions, priestly
  politics. Forms: organized church, ancestral rite systems, temple cults,
  household observance webs.
- **Heresy / cult** (regional, spawnable) — following, secrecy, fervor ·
  misery +, orthodox pressure − · whispered meetings, strange symbols, a
  charismatic preacher on the move.
- **Pilgrimage route** (regional) — pilgrim flow, shrine prestige · roads
  +, miracle rumours + · badge-sellers, hostels, relic politics.
- **Festival calendar** (regional) — next feast, scale · season clock,
  harvest + · preparations, truces, markets timed to feasts.
- **Devotional order** (regional) — houses, lands, discipline · donations +,
  scandal − · learning kept and copied, hospitality, land disputes with
  neighbours. Forms: monasteries, teaching houses, ascetic communities,
  mission stations.
- **Omen climate** ⚝-lite (world) — anxiety level, current omens ·
  celestial events +, disasters + · sermons, scapegoats, soothsayers doing
  brisk trade. Works even in no-magic settings: omens need only be believed.

### 3.7 Word and renown
- **Rumour network** (world; near-mandatory core) — carries *specific
  claims* (each with truth flag and origin) along roads and rivers at
  travel speed · routes +, markets +, war − · what innkeepers know, how
  distorted it is by distance, news arriving with the player or before
  them. This is the mechanism behind KN-5 and catch-up "news".
- **Player renown** (regional-per-region; core) — recognition, repute
  valence, deeds attached · rumour network carries it; deeds feed it ·
  being recognized, prices shifting, doors opening or closing, songs. The
  player's fame is *modeled as a subsystem*, not scripted.
- **Manhunt / pursuit** (regional, spawnable) — pursuer resources, trail
  heat · crimes +, renown +, distance − · wanted notices, questions asked
  after you, roadblocks at fords.
- **Bardic circuit** (regional) — repertoire, circuit route · renown +,
  patronage + · songs that arrive before you, embellishment, a bard who
  wants *your* story.
- **Scholarly network** (world, slow) — corresponding savants, questions
  in fashion · patronage + · letters seeking specimens, expeditions, prizes
  for answers.

### 3.8 Shadow trades
- **Smuggling network** (regional) — volume, routes, discipline · tolls +
  (cause), patrols −, marsh/terrain cover + · night boats, cheap untaxed
  goods, locals who don't see anything.
- **Criminal fraternity** (local, urban) — organization, turf, heat ·
  wealth +, watch − · protection offers, fenced goods, a tavern where
  questions stop.
- **Black market** (local) — availability of the forbidden · scarcity +,
  law − · back-room prices, coded signs, entrapment risk.
- **Vice economy** (local) — gambling/drink depth, debts held · garrisons
  +, misery + · card debts, watered wine, a debtor's desperate offer.
- **Counterfeiting** (regional, spawnable) — quality, spread · currency
  trust − (target) · suspicious coin, assay tests, a hunt for the plates.

### 3.9 Works and ways
- **Road state** (regional; couples to the road tiles) — condition per
  stretch, maintenance · lordship +, weather −, traffic − · ruts, wash-outs,
  corvée gangs; travel-speed effects the player feels directly.
- **Crossings** (local) — bridge/ford/ferry status · floods −, tolls,
  neglect − · queues, detours, the ferryman's monopoly.
- **Harbor and shipping** (regional, coastal) — berth traffic, pilotage,
  storm losses · trade +, piracy − · masts in the roads, chandlers, sailors'
  gossip (rumour accelerant).
- **Mills and irrigation** (local) — capacity, water rights · river flow +,
  disputes − · grinding queues, sluice sabotage, miller's thumb jokes.
- **City sanitation** (local, urban) — filth level · population +, works − ·
  smell, rats (couples to vermin → plague), night-soil men.
- **Fortifications** (local) — wall/keep condition, stores · treasury +,
  peace − (neglect) · scaffolding or ivy, complacent watchmen, a breach
  everyone knows about.
- **Beacon / signal network** (regional) — readiness · garrison + · wood
  stocked on hilltops, watchers' huts, false alarms.

### 3.10 Ail and remedy
- **Epidemic** (regional, spawnable; the big one) — spread extent,
  virulence, mortality · vermin +, sanitation −, trade + (carrier),
  quarantine − · closed gates, bells, empty markets, flight of the rich.
- **Endemic disease** (local) — background sickness rate · miasma +,
  remedies − · seasonal fevers, local immunity, herb-wives' trade.
- **Healing craft** (regional) — practitioner supply, knowledge level ·
  monasteries +, war + (grim practice) · barber-surgeons, herb gardens,
  fees and quackery.
- **Madness / despair climate** (local, slow) — communal morale · famine −,
  war −, festivals + · empty taverns or full ones, roadside shrines,
  kindness or its absence.

### 3.11 Contact and migration
For settings where peoples, economies, or cosmologies meet — frontiers,
diasporas, colonizations, first contact. The most coupling-hungry family in
the catalog: nearly every entry here modulates entries elsewhere.

- **Cultural frontier** (regional) — mutual intelligibility, protocol-breach
  risk, intermarriage depth, go-between supply · trade +, incidents − ·
  interpreters and culture-brokers prospering, gift exchanges gone right or
  catastrophically wrong, households of two worlds.
- **Conversion contest** (regional) — adherence shares, momentum, syncretism
  level · established observance ±, misery +, literacy/novelty + · rival
  officiants, first converts celebrated or shunned, old rites held in secret,
  hybrid practices nobody planned.
- **Arms revolution** (regional/world) — new-weapon diffusion, balance
  upset, arms-race pressure · trade routes + (supply), wars + · one-sided
  battles, desperate trading for the new weapon, old fortifications and old
  tactics suddenly obsolete, power maps redrawn.
- **Ecological invasion** (regional, slow) — introduced-species spread,
  native decline, transformation of livelihoods · ships/trade + (vectors) ·
  new animals in the bush, old food sources failing, landscapes visibly
  changing within a lifetime.
- **Migration and settlement** (regional) — population flows, land pressure,
  welcome/resistance · wars +, famine +, opportunity + · newcomers' camps,
  land disputes, mixed settlements, homesick songs.
- **Frontier entrepôt** (local) — lawlessness, traffic, protection
  arrangements · trade +, authority − (none reaches here) · grog shops,
  every language at once, fortunes and throats cut, everyone under someone's
  protection whether they know it or not.
- **Kinship web** (regional; core in kin-ordered societies) — genealogical
  obligation density, alliance state · marriages +, feuds − · who must
  shelter you, who must avenge you, introductions that open every door or
  none.
- **Obligation economy** (regional) — gift debts outstanding, reciprocity
  balance, prestige stakes · kinship +, wealth + · feasts that beggar the
  host and bind the guests, unpayable generosity as power, ledgers kept in
  memory.
- **Sacred restriction system** (regional; NOT inherently supernatural) —
  restriction observance, violation count, specialist authority · observance
  +, contact friction + · places and things not touched, foods not eaten,
  persons not approached; violations demanding costly restoration; law that
  behaves like physics whether or not anything enforces it.

### 3.12 The uncanny ⚝ (optional family — include only if the setting says so)
- **Wild magic zone** — intensity, drift extent · ley events + · warped
  growth, compass trouble, hedge-wizards drawn like moths.
- **Haunting / curse** (local, spawnable) — strength, anchor, appeasement ·
  desecration +, rites − · cold rooms, avoided paths, a price for lifting it.
- **Waking ruin** — stirring level, seals · trespass +, omens + · lights in
  old windows, missing explorers, artifacts surfacing at market.
- **Otherworld crossings** — permeability, schedule (solstices) · rites ±,
  season clock · fairy bargains, lost time, roads that aren't there by day.
- **Ley economy** — flow, node health · use −, rituals + · practitioner
  migration, node pilgrimages, magical price inflation.
- **Belief economy** — per-being or per-pantheon: observance level, believer
  count, potency · established observance +, conversion contest ±, migration
  + (beings travel with their people, arriving thin and hungry) · beings
  strengthening where honoured and fading where forgotten, small gods doing
  desperate things for attention, old powers of the land confronting
  newcomers' spirits. The "gods walk among migrants" engine.
- **Spirit road** — traffic of the dead, waypoint sanctity, blockages ·
  belief economy +, desecration − · funeral customs oriented along it,
  places the dead pass through, the unquiet dead of those buried wrong or
  far from home.
- **Hidden folk** — population, seclusion, tolerance of humans · settlement
  − (they withdraw), observance of old courtesies + · liminal sightings at
  dusk and mist, changeling fears, gifts left out, rules (never thank them,
  never trade names) that locals keep without remembering why.

### 3.13 Persons and fates (engine-native)
- **Personal arc (T3)** (local, mobile) — the promoted-NPC subsystem
  (SS-3, SIM-4): goals, means, mood, momentum · couples to whatever their
  life touches, always to the player-thread · them turning up changed,
  word of their doings, their agenda advancing off-screen.
- **Household / dynasty** (regional) — heirs, wealth, standing · marriage
  market +, feuds − · betrothals, funerals, inheritance disputes.
- **Prophecy / doom clock** ⚝-lite (world, storyteller-owned) — a dated or
  conditioned future the storyteller schedules (ND-4) · omens +, player
  deeds ± · signs accumulating, cults forming, the date approaching.

## 4. Authoring checklist (SS-14)

A subsystem file passes review only if:
1. All five SS-1 parts present; 2–5 typed variables (SS-5).
2. Baseline drift defined (SS-6) — it moves even when ignored.
3. ≥1 threshold event with visibility set.
4. ≥2 couplings, each with sense/strength/latency (SS-7); at least one
   *outgoing* (it pushes the world, not just reacts).
5. Footprint overlaps ≥1 other subsystem's (SS-4); index updated (SS-13).
6. ≥3 manifestations across ≥3 channels; fairness rule satisfied; signature
   written (SS-9).
7. Scale + status set (SS-10, SS-11); ledger present (SS-12).

## 5. Recommended starter set (for M2's pocket region)

Two always-on cores plus five setting picks that interlock tightly:

- Core: **Season clock**, **Rumour network** (+ **Player renown** as soon as
  the player does anything worth talking about).
- Setting: **Predator population** (wolves, hard winter), **Harvest cycle /
  food distribution** (one merged instance at pocket scale), **Smuggling
  network** (the marsh), **Lordship authority** (the wardens of the keep),
  **Orthodox faith** (the drowned chapel's congregation and its unease).

Why these: every pair couples (wolves↔lordship via patrols, smuggling↔faith
via the chapel, harvest↔wolves via herds, everything↔season, rumour carries
all of it), every area sits in 2+ footprints (SS-4), and all six VA seams can
be tested against them without authoring anything else.
