# open-pptd-skill

中文版: [README.md](README.md)

The **content plane** (knowledge pack) for [open-pptd](https://github.com/Shingwha/open-pptd) — a
presentation creation and export skill built around the **PPTD format**, a YAML intermediate DSL
that abstracts OOXML into self-contained pages.

This repository is pure text: `SKILL.md` plus the `references/` it links to. It contains **no
executable files** — no scripts, no build step, no dependencies. All preview, validation, render,
and export actions are performed by the `open-pptd` command-line tool from the engine repository.

```
open-pptd-skill/
├── SKILL.md                    # the skill entry point (methodology + CLI command surface)
├── references/
│   ├── design.md               # scenario guide, visual styles, palette, font system
│   ├── pptd.md                 # the PPTD v2 format spec (single source of truth)
│   ├── shapes.md               # preset shape lookup table (177 shapes + parameters)
│   └── slides_categories/      # per-scenario deep dives (×8)
├── README.md
├── LICENSE
└── .github/workflows/drift-guard.yml
```

## 1. Install the CLI first

The skill only knows the **command surface** of the CLI; it cannot draw, check, or export on its
own. Install the CLI once per machine (into `~/.open-pptd`, no administrator needed):

**Windows (PowerShell)**

```powershell
irm https://raw.githubusercontent.com/Shingwha/open-pptd/main/install.ps1 | iex
```

**Linux / macOS**

```sh
curl -fsSL https://raw.githubusercontent.com/Shingwha/open-pptd/main/install.sh | sh
```

The installer downloads the latest runtime zip from GitHub Releases, verifies its SHA256, unpacks it
to `~/.open-pptd/cli/versions/<ver>`, adds `~/.open-pptd/cli/bin` to the **user-level** PATH, and
installs the Font Awesome icon assets by default. It is idempotent and can be re-run safely.
Options: `-Version <ver>` (PowerShell) / `--version <ver>` (sh) to pin a version, and
`-WhatIf` / `--dry-run` to preview the steps without touching the system.

Verify the installation:

```sh
open-pptd doctor
```

If `open-pptd` is not found, open a new terminal first (PATH changes do not affect already-running
processes).

## 2. Install this skill

Copy (or clone) this repository into the skills directory of the agent you use, so that the skills
directory contains an `open-pptd/` folder holding `SKILL.md` and `references/` together:

```sh
git clone https://github.com/Shingwha/open-pptd-skill.git <your-skills-dir>/open-pptd
```

Typical locations (adjust to your agent):

| Agent | Skills directory (example) |
|---|---|
| ZCode | `~/.zcode/skills/` |
| pi / others | the skills directory configured for that agent |
| Claude Code | the project/session skills folder |

The requirement is only that `SKILL.md` and `references/` stay in the **same directory** — that is
the standard Agent Skills layout. `SKILL.md` references `references/*` with relative paths.

> Installing the skill does **not** install the CLI, and vice versa. This split is deliberate: the
> engine evolves on its own release cadence, while this skill only depends on the stable command
> surface (`serve` / `check` / `export` / `ensure` / `render` / `assets` / `fonts` / `doctor` / …).

## 3. Drift guard

`references/` is hand-written prose, but it describes contracts that live in the engine code. The
`drift-guard` workflow (`.github/workflows/drift-guard.yml`) pulls the latest engine release and
performs five read-only comparisons against it (the first four are the mandated ones):

1. `pptd.md §5` per-type required fields ↔ the engine's `validate.js` schema key set;
2. `pptd.md §3` Theme structure ↔ `theme.js` / `theme-presets.js` key names;
3. `shapes.md` shape/connector counts ↔ `scripts/gen-preset-geometry.mjs` geometry data counts;
4. `design.md §4` registered font names ↔ `assets/fonts/registry.json` family/alias set;
5. `design.md` preset color tables ↔ `theme-presets.js` `THEME_PALETTES` (the guard inherited from the
   engine's former `tests/regression/theme-presets.mjs`, kept here now that the doc lives in this repo).

Any mismatch fails the workflow with a message that `references/` must be updated to match the
engine. The check is purely read-only and introduces no dependency between the two repositories.

## License

MIT — see [LICENSE](LICENSE).
