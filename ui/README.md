# Twin UI — golden-path widget templates

These files are the canonical, human-owned widget implementations. Claude renders
them verbatim, injecting only game state — it never redesigns them on the fly.
Consistency comes from these files, not from the model.

## How injection works

Each template contains exactly one slot:

```js
const STATE = /*__STATE__*/null;
```

At render time Claude replaces `/*__STATE__*/null` with a JSON state object
(schema: `docs/ENGINE-SPEC.md`, UI-4) and ships the whole file as the widget. When the slot is left as `null` — e.g. when you open the file directly in
a browser or the preview panel — the template falls back to its built-in
`DEMO_STATE`, and `sendPrompt` calls log to the console instead. So the file is
its own test harness: edit, refresh, iterate, no server needed.

## Editing workflow

1. Open `travel-map.html` in a browser (or the Launch preview panel).
2. Edit styles/rendering; refresh to check. Tweak `DEMO_STATE` to exercise cases:
   fog of war, a route in progress, rumoured-but-unexplored places, 0/4 stat chips,
   long place names.
3. Check both light and dark backgrounds. The parchment panel is deliberately
   self-coloured (a physical object, same in both modes), but anything outside the
   frame must use CSS variables.
4. That's it — the next time Claude renders the map, your changes are live.

## Widget environment constraints (learned the hard way, keep respecting them)

- Container is ~680px wide; height auto-fits content. No `position: fixed`.
- External resources only from: cdnjs.cloudflare.com, esm.sh, cdn.jsdelivr.net,
  unpkg.com, fonts.googleapis.com, fonts.gstatic.com. Everything else is blocked.
- Scripts execute only after the widget finishes streaming in.
- `sendPrompt(text)` is a global in the real widget host; always guard it for
  local preview (the `say()` helper does this).
- Each render is a fresh snapshot — no state survives between turns. All state
  must arrive via the STATE slot.
- Fonts: IM Fell English / IM Fell English SC via Google Fonts, serif fallback.
