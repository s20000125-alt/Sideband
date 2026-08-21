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
| [logs/](logs/) | One session log per working session — what changed, how, and what verified it — plus the screenshots they cite. [logs/README.md](logs/README.md) indexes them. |
| [assets/breadboard/](assets/breadboard/) | `NanofiberMOTAssembly.STL`, the source geometry the MOT-chamber outline, keep-out radius, and ⌀60 through-hole were traced from. Provenance only — the app does not load it at runtime. |
| [assets/reference/](assets/reference/) | Layout reference images (board-6 schematic export, AOD/SLM full layout). |
| [tools/](tools/) | Unrelated to the simulator: [Fix-ClaudeSessionHistory.md](tools/Fix-ClaudeSessionHistory.md) + `.ps1` fix empty Claude Code session history on Windows machines whose projects live on a mapped network drive. |

## Working conventions

- Edits go into `simulator/simulator.html` only. Two rejected attempts at the same
  change → restore that section from `simulator_original.html` verbatim and re-ask.
- One commit per verified edit round, so `simulator_original.html` stops being the
  only way back.
- **Engine conventions, how to verify an edit, and the gotchas are in
  [simulator/HANDOVER.md](simulator/HANDOVER.md) — that file is the single source.**
  This README deliberately does not restate them: it used to, and both copies of the
  verification method went stale within one session.
- **Nothing is left untracked**: [tools/git-autocommit.sh](tools/git-autocommit.sh)
  runs from a Claude Code `Stop` hook (`.claude/settings.json`) and commits any
  outstanding change — yours or Claude's — as `auto: N file(s) changed`. It never
  pushes and skips a repo mid-merge/rebase. Run it by hand any time; disable it by
  removing the hook (review via `/hooks`).
- The repo is **local only** — no remote is configured, so this is version history,
  not a backup.
