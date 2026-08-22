# MissAlign

**A browser-based optical path designer for tabletop laser labs.** Lay out an optical
bench on screen, trace the beam through it with the physics you would use on paper, and
read off the numbers you would otherwise measure with a beam profiler — before you touch
a single mount.

Astigmatic (two-axis) Gaussian propagation over a 25 mm breadboard grid, with Jones-calculus
polarisation, fiber channels and coupling efficiency, AOM orders, out-of-plane periscopes,
the nanofiber-MOT chamber, and publication-quality schematic export.

## Run it

Open [simulator/simulator.html](simulator/simulator.html) in a browser. That is the whole
installation — one self-contained file, no build step, no server. The live scene autosaves
to `localStorage`; hard-reload (Ctrl+F5 / Cmd+Shift+R) after editing the file.

## Start here

| | |
|---|---|
| 📖 **[MANUAL.md](MANUAL.md)** | **How to use it** — controls, components, and worked recipes (place a waist, couple a fiber, circularise a beam, route a periscope). |
| 💡 **[ABOUT.md](ABOUT.md)** | What MissAlign is, what it was before, and what was added to it. |

## Layout

| Path | What |
|---|---|
| [ABOUT.md](ABOUT.md) | Project introduction: capabilities, origin, and the contributions on top of it. |
| [MANUAL.md](MANUAL.md) | **User manual** — the place to start if you just want to use it. |
| [simulator/](simulator/) | The app. `simulator.html` is the **working file — all edits go here**; `simulator_original.html` is the pristine upstream copy, **never edited**, used to restore a section verbatim when a change is rejected. `index.html` is a landing page. |
| [simulator/HANDOVER.md](simulator/HANDOVER.md) | Engine conventions, verification workflow, and the gotchas worth not rediscovering. **Read first when picking the project back up.** |
| [simulator/CHANGELOG.md](simulator/CHANGELOG.md) | What changed in each work session, 2026-08-07 onward. |
| [logs/](logs/) | One session log per working session — what changed, how, and what verified it — plus the screenshots they cite. [logs/README.md](logs/README.md) indexes them. |
| [LICENSE](LICENSE) | MIT. Two copyright holders — see [ABOUT.md](ABOUT.md) for who wrote what. |
| [LOCAL-FILES.md](LOCAL-FILES.md) | **What is deliberately not in this repo** — `assets/`, `scenes/` and `tools/` live on disk and the NAS, per the lab handbook's rule on 3D exports and measurement data. Read this if a doc links a path you do not have. |

## Working conventions

- Edits go into `simulator/simulator.html` only. Two rejected attempts at the same
  change → restore that section from `simulator_original.html` verbatim and re-ask.
- One commit per verified edit round, so `simulator_original.html` stops being the
  only way back.
- **Engine conventions, how to verify an edit, and the gotchas are in
  [simulator/HANDOVER.md](simulator/HANDOVER.md) — that file is the single source.**
  This README deliberately does not restate them: it used to, and both copies of the
  verification method went stale within one session.
- **Nothing is left untracked**: `tools/git-autocommit.sh` (local-only, see
  [LOCAL-FILES.md](LOCAL-FILES.md))
  runs from a Claude Code `Stop` hook (`.claude/settings.json`) and commits any
  outstanding change — yours or Claude's — as `auto: N file(s) changed`. It never
  pushes and skips a repo mid-merge/rebase. Run it by hand any time; disable it by
  removing the hook (review via `/hooks`).
- The repo is **local only** — no remote is configured, so this is version history,
  not a backup.

## Hardware and dependencies

None on both counts, which is the point. `simulator/simulator.html` is one
self-contained file: no build step, no server, no package manager, no vendor driver.
Any modern browser runs it. It talks to no instrument — it models the bench rather
than driving it, so there is nothing to configure and no `.env` to fill in.

## Known broken / gotchas

The full list, with the reasoning behind each, is in
[simulator/HANDOVER.md](simulator/HANDOVER.md). The ones most likely to cost you time:

- **Hard-reload after editing the file.** The live scene autosaves to `localStorage`
  and the browser caches the HTML aggressively. Ctrl+F5 / Cmd+Shift+R, or you will be
  looking at your previous edit and drawing wrong conclusions from it.
- **`simulator_original.html` is never edited.** It is the pristine upstream copy and
  the only way back when a change has to be reverted verbatim. All edits go in
  `simulator.html`.
- **Beam sizes are 1/e² radii**, quoted as `w`. Diameters are written `⌀ = 2w`. Mixing
  the two silently gives you a factor-of-2 error in every aperture check.
- **World units are millimetres** everywhere, including the fine-nudge controls.
- **`beam.qa` is an offset, not an absolute.** The out-of-plane axis is
  `q_vertical = q + qa`; only elements with optical power remap it. A scene with no
  cylindrical optics has `qa == null` throughout.
- **A periscope that turns 90° in azimuth exchanges the two transverse axes.** This is
  modelled deliberately, so an astigmatic beam through a periscope is not a bug.
- **There is no build step to catch a syntax error.** Parse every inline script after
  editing; a typo yields a blank page, not a stack trace.
- **Docs may name files you do not have.** `assets/`, `scenes/` and `tools/` are
  local-only — see [LOCAL-FILES.md](LOCAL-FILES.md).

## Provenance

The original simulator was written by a labmate, @s20000125-alt, with LLM assistance and is
published at https://github.com/iams-yb-lab/frequency_shift_simulator. It already had
the single-file bench, Jones-calculus polarisation, `q`-parameter Gaussian propagation,
19 component types and the analysis tabs. The two-axis (astigmatic) engine, out-of-plane
periscope routing, fiber collimators and per-axis coupling, the `w(z)` caustic
instrument, publication schematic export, sub-hole fine alignment and the nanofiber-MOT
board were added by Yi-Cheng "Maximus" Liu (@yi-cheng-maximus-liu). The full before/after
account is in [ABOUT.md](ABOUT.md); per-session detail is in
[simulator/CHANGELOG.md](simulator/CHANGELOG.md) and [logs/](logs/).

[LICENSE](LICENSE) is MIT and names one copyright holder, covering the contributions
described above; the upstream repo carries no licence of its own, and this repo does not
relicense it on its behalf.

**Maintainer:** Yi-Cheng "Maximus" Liu.
