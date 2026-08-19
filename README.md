# IAMS Yb Lab — optics layout simulator

Beam-path and breadboard layout tool for the Yb atomic-physics lab: Gaussian-beam
propagation (q-formalism, 1/e² radii, mm world units) over a 25 mm breadboard grid,
with fiber channels, AOM orders, the nanofiber-MOT chamber, and publication-quality
schematic export.

## Run it

Open [simulator/simulator.html](simulator/simulator.html) in a browser — it is a
single self-contained file, no build step and no server. Hard-reload (Ctrl+F5 /
Cmd+Shift+R) after editing it; the live scene autosaves to `localStorage`.

## Layout

| Path | What |
|---|---|
| [simulator/](simulator/) | The app. `simulator.html` is the **working file — all edits go here**; `simulator_original.html` is the pristine upstream copy, **never edited**, used to restore a section verbatim when a change is rejected. `index.html` is a landing page. |
| [simulator/HANDOVER.md](simulator/HANDOVER.md) | Engine conventions, verification workflow, and the gotchas worth not rediscovering. **Read first when picking the project back up.** |
| [simulator/CHANGELOG.md](simulator/CHANGELOG.md) | What changed in each work session, 2026-08-07 onward. |
| [scenes/](scenes/) | Saved lab layouts (`.json`). [scenes/README.md](scenes/README.md) is the timeline — newest layout is the last row. |
| [assets/breadboard/](assets/breadboard/) | `NanofiberMOTAssembly.STL`, the source geometry the MOT-chamber outline, keep-out radius, and ⌀60 through-hole were traced from. Provenance only — the app does not load it at runtime. |
| [assets/reference/](assets/reference/) | Layout reference images (board-6 schematic export, AOD/SLM full layout). |
| [tools/](tools/) | Unrelated to the simulator: [Fix-ClaudeSessionHistory.md](tools/Fix-ClaudeSessionHistory.md) + `.ps1` fix empty Claude Code session history on Windows machines whose projects live on a mapped network drive. |

## Working conventions

- Edits go into `simulator/simulator.html` only. Two rejected attempts at the same
  change → restore that section from `simulator_original.html` verbatim and re-ask.
- No Node on this machine. Verify an edit round with a Python brace/backtick balance
  check against the original (baseline raw-count offsets: `()` = −2, `[]` = +1) and,
  where behavior matters, headless Chrome over CDP against a real scene.
- One commit per verified edit round, so `simulator_original.html` stops being the
  only way back.
