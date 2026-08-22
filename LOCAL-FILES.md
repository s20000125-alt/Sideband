# Files kept out of this repo

The [lab handbook](https://github.com/iams-yb-lab/lab-handbook) keeps 3D exports and
measurement data out of git — version the code, and document a path to the data. Three
directories therefore live on disk and on the NAS, but are **not** tracked here and have
been purged from this repo's history. They are listed in [`.gitignore`](.gitignore), so
they will not be re-added by accident.

| Directory | What is in it | Why it is not in git |
|---|---|---|
| `assets/breadboard/` | `NanofiberMOTAssembly.STL` (26 MB) — the assembly the MOT-chamber outline, keep-out radius and ⌀60 through-hole were traced from. Provenance only; the app never loads it at runtime. | A 3D geometry export. The handbook routes these to a tagged release or the NAS, never git. |
| `assets/reference/` | Layout reference images: board-6 schematic export, AOD/SLM full layout. | Lab-internal reference material. |
| `scenes/` | ~20 saved `.json` bench layouts of the real Yb lab, plus a `README.md` timeline. | Lab layout records — internal measurement/configuration data rather than code. |
| `tools/` | `Fix-ClaudeSessionHistory.{md,ps1}` (unrelated to the simulator) and `git-autocommit.sh` (the `Stop`-hook autocommit script). | Local developer tooling, not part of the simulator subsystem. |

## Where to get them

- **On the original machine:** they are already in place, ignored by git.
- **Backup copy:** `~/Desktop/optical-path-designer-localonly-backup/`
- **NAS:** `TODO — record the NAS path here once the directories are filed.`

Restoring them is a plain copy into the repo root; nothing needs to be registered with
git, and the app runs without them (the STL and reference images are documentation, and
scenes load through the app's own Load button from wherever you keep them).

## Consequences worth knowing

- The `Stop`-hook autocommit described in [README.md](README.md) needs
  `tools/git-autocommit.sh`, which is **not** in a fresh clone. Copy `tools/` in, or drop
  the hook from `.claude/settings.json`.
- Doc references to a scene file (for example in
  [`simulator/CHANGELOG.md`](simulator/CHANGELOG.md) and
  [`simulator/HANDOVER.md`](simulator/HANDOVER.md)) name the regression scene by filename.
  Those names are stable; the files just live outside the repo.
