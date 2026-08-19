# Handover — frequency_shift_simulator (as of 2026-08-20, rev 3)

## Files & rules
- `simulator/simulator.html` — the WORKING file (all edits go here). Single self-contained
  HTML app, ~13.5k lines, one main `<script>` block. `index.html` is a landing page only.
- `simulator/simulator_original.html` — pristine original. **Never edit.** Used to restore
  sections verbatim when a change is rejected ("go back to the original X code").
- No Node on this machine; verify edits with a Python script comparing brace/backtick balance + difflib hunks
  between original and working file (baseline raw-count offsets: `()` = −2, `[]` = +1).
- User tests in the browser; remind them to hard-reload (Ctrl+F5). Scenes autosave to localStorage.

## Engine conventions
- World units = mm; canvas y points down. All beam sizes are 1/e² intensity RADII ("⌀" = 2w everywhere).
- Gaussian q-formalism: `makeGaussianQFromLaser`, `qPropagateFree`, `qApplyABCD`, `beamRadiusFromQ`.
  Laser waist floor = λ/2 (three hidden 5 µm clamps removed).
- **`beam.q` is the IN-PLANE (board-plane) axis.** The out-of-plane (vertical) axis is `beam.qa`, an
  OFFSET: `q_vertical = q + qa`, `null`/absent = round. Stored as a difference because that is exactly
  invariant under free propagation, so all `{...beam}` spreads and every `qPropagateFree` site need no
  extra bookkeeping. Only elements with POWER touch it: `_qaThroughShared` (spherical lens, curved
  mirror — same ABCD both axes), `_qaFromAxisLens` (cylindrical, one axis), fiber → `null`, linked
  V-mirror → `_vmQaAfterLink`. Read the vertical axis with `qVertOf(q, qa)` / `beamRadiusVertFromQ`;
  test with `_qaIsRound(qa)` or `isAstigmatic(beamOrSegOrHit)`. Segments carry ONE `qa` (invariant along
  the segment); `_lastHit` carries `qa` + `beamRadiusVertMm`. **If you add an element with optical power,
  remap `qa` there or astigmatism goes subtly wrong downstream of it.**
- Tracer stores a beam object for every PREFIX of a path; use the longest continuation when mapping a component
  to its beam/z (`_compBeamZ`). `PUSH = 8` mm gap after every optic (q kept consistent) — this leaves 8 mm HOLES
  in path-length coverage; `_beamPosAtPathLen` tolerates gaps ≤10 mm (just above PUSH) and clamps in-hole z to
  the nearest segment edge. FiberIn absorbs → paired FiberOut (same channel, `(c.channel||'A')`) re-emits;
  fiber resets z and beam quality.
- Interaction apertures are decoupled from drawn/3D housing sizes: fiberin/fiberout ±10 mm, AOM ±10 mm box
  (AOM physics otherwise the untouched original).

## Features added this session
Channel pairing UI (A–H dropdown + status), NA ↔ mode-field-radius controls on both fiber ends, collimator &
coupling-lens fine-tune (axial/lateral nudges + typed distance), two-component separation ruler with typed
distance, persistent groups (click=group, Alt+click=member, ⛓gN badges, 25 mm hole snapping incl. best-fit
rigid re-land after rotation), linked probes (w(z) plot ↔ board marker, both directions), z-coordinates in the
caustic optics bar and an editable "z on beam" panel row, caustic range selectors that keep user choices
(dataset.prevVal) and accept endpoints in either order, hover-only component labels/badges (`_hoverComp`,
`_labelVisible`), adaptive w(z) sampling around µm-scale waists, µm-aware readouts in the laser q panel.

## Features added 2026-08-20
Cylindrical lens component (`cylens`) with a selectable powered axis (in-plane / out-of-plane) and full
lens parity, plus the astigmatic two-axis beam tracking described above: dual-envelope w(z) plot,
astigmatism-aware fiber coupling (η = η₁D·η₁D per axis), periscope axis exchange, `w_v` export columns.
See CHANGELOG.md for the full list and the verification evidence.

## Gotchas that burned time (don't rediscover)
1. **CSS uppercase turns "µ" into Greek capital Mu → "(µm)" renders "(MM)".** Fixed with
   `.prop-label .unit{text-transform:none}` + `<span class="unit">µm</span>`. Use for any new µ label.
2. **w(z) plot sampling** is 10 samples/mm — without the adaptive waist cluster, a 1.5 µm waist plots as ~5–10 µm
   ("looks like a clamp bug").
3. **Panel readouts in fixed mm** rounded fiber-scale values to nonsense (0.002 mm); use the adaptive µm/mm
   formatter pattern.
4. **render() invalidates `_leafBeamsCache`** → caustic selectors rebuild constantly; any user choice there must
   persist via `dataset.prevVal`.
5. **Spatial change requests:** restate a concrete canvas-level example ("component at 0°, beam at X should Y")
   before coding; prefer the smallest fix; two rejections → revert to original verbatim and re-ask. (AOM lesson.)
6. **PUSH=8 path holes:** any tolerance/lookup working in path-length coordinates must allow >8 mm slack, or
   valid positions right behind an optic get rejected (caused false "z beyond traced path" popups when editing
   lens positions — fixed by raising `_beamPosAtPathLen` gap tolerance 2→10 mm).
7. **A cylinder powered out of plane deliberately does nothing visible in top view** — not a bug: it
   changes neither the drawn beam width nor the chief-ray direction (a vertical kick is unrepresentable
   in a top-view engine). Its effect shows in the w(z) plot's dashed envelope, the panel's out-of-plane
   rows, and the fiber-coupling ellipticity. Say this before "fixing" it.
8. **Test helper trap:** the tracer stores a beam object for every PREFIX of a path, and prefixes carry
   the SAME total power — so picking the "primary" beam by power alone silently returns a 1-segment stub
   and every downstream measurement reads "no data". Pick by longest path (`segments[last].pathEndMm`).
9. **Regression proof that works here:** fingerprint a real scene (all segment geometry + q + coupling
   η + `_lastHit` readouts) through headless Chrome on the pre-change copy and the working file, and
   diff. `scenes/IAMS_Yb_Lab_2026-08-19_1152.json` (179 comps, 255 beams) exercises lenses, AOMs,
   fibers, and V-mirrors at once. Tooling from the cylindrical-lens session is in the session scratchpad
   pattern: minimal raw-socket CDP client (no pip deps) + `osascript -l JavaScript` for parse checks —
   there is still no Node on this machine.

## User context
Yb atomic-physics lab; fluent in Gaussian optics — communicate in those terms. Typical parameters: 460 nm,
PM460-HP-like fiber (w_f ≈ 1.5–1.65 µm), AOM double-pass, 25 mm breadboard grid. Wants minimal, lab-realistic
changes and an uncluttered canvas.
