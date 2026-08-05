# Agent Cheat Sheet

Quick reference for routing work to the right specialist in `.claude/agents/`.
See individual `.md` files in this folder for full personas.

## Quick lookup

| I want to... | Agent |
|---|---|
| Add/expand items in `foods.json` or `vitamins.json` | `data-builder` |
| Build or change any screen, form, modal, nav, or CSS | `ui-builder` |
| Fix offline caching, install prompts, manifest fields | `pwa-specialist` |
| Add logging, calculations, or localStorage read/write logic | `log-engine` |
| Audit or test before shipping a feature (read-only) | `qa-reviewer` |

## The five agents

| Agent | Owns | Tools | Model |
|---|---|---|---|
| `data-builder` | `foods.json`, `vitamins.json` | Read, Write, Edit, Bash | haiku |
| `ui-builder` | `index.html` markup/CSS | Read, Write, Edit | sonnet |
| `pwa-specialist` | `sw.js`, `manifest.json` | Read, Write, Edit, Bash | sonnet |
| `log-engine` | JS logic in `index.html` | Read, Write, Edit | sonnet |
| `qa-reviewer` | nothing — reports only | Read, Grep, Glob, Bash | haiku |

## Coordination rules

**Safe to run in parallel:**
- Any two agents writing *different* files (e.g. log-engine on `index.html` + pwa-specialist on `sw.js`)
- `qa-reviewer` alongside anything — it's read-only
- Two `data-builder` runs on different files

**Must run sequentially:**
- Two agents that would both write `index.html` at once — real lost-write risk. Have them return proposed diffs instead of writing directly, then merge by hand.
- UI work that depends on new data-layer functions — run `log-engine` first, get its exact function names/shapes, then brief `ui-builder` with that contract.
- Multiple `data-builder` runs on the *same* file with overlapping id ranges.

## Known limitation

Custom `subagent_type` values (`"ui-builder"`, etc.) haven't been recognized in sessions so far this project — only the fixed built-ins (`general-purpose`, `Explore`, etc.) resolve, even though these `.md` files exist correctly on disk. **Workaround:** launch `general-purpose` and paste the target agent's persona from its `.md` file into the prompt. Worth retesting the native name in a fresh session — this may just be a stale per-session agent list.

## Real examples from this project

- **data-builder:** "Add these 9 Kirkland protein/fat items to foods.json, following the existing schema — flag anything you can't verify against a real label."
- **ui-builder:** "Add a Supplements section: nav item, category-grouped list, log modal. Here's the exact log-engine contract to build against: `loadVitamins()`, `logSupplement(id, dose, unit)`, `getDailyGoalProgress()` → [...]."
- **log-engine:** "Add `exportAllDataAsJSON()` and `getWeeklyMacroSummary()` to index.html — reuse `todayKey()`/`computeTotals()`, never round-trip a date string through `new Date()`."
- **pwa-specialist:** "Add vitamins.json to the service worker precache list and bump the cache version so installed devices pick it up."
- **qa-reviewer:** "Audit foods.json, vitamins.json, index.html, sw.js. Report CRITICAL / WARNING / SUGGESTION, read-only."

## Golden rule

Agents self-report success — this project has caught real bugs their own reports missed. Always verify yourself before shipping: read the actual file, run a syntax check, and for anything touching the UI, load it in a real browser and click through it.
