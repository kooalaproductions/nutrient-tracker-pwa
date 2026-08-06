> ⚠️ IMPORTANT: Claude Code must always be launched from:
> C:\Food_Nutrient_Tracker\pwa
>
> Run `pwd` to verify before starting any task.
> Run `ls .claude/agents/` to confirm agents are accessible.
> If agents folder is missing, you are in the wrong directory.

# Nutrient Tracker PWA

Single-file vanilla HTML/CSS/JS nutrition tracker. No build step, no framework,
no backend — see `index.html`, `foods.json`, `manifest.json`, `sw.js`.

Deployed via GitHub Pages: https://kooalaproductions.github.io/nutrient-tracker-pwa/
(rebuilds automatically on push to `master`).

## Workflow

Whenever a change is made to this project (bug fix, feature, tweak), commit and
push it to `master` as part of finishing that task, instead of leaving it as an
uncommitted local edit. This applies to normal user-requested changes — it is
not standing permission to make unrequested changes on your own initiative.

## Available Specialist Agents

These agents live in `.claude/agents/` and are auto-available every session.

| Agent | Role | When to use |
|---|---|---|
| `data-builder` | foods.json / vitamins.json | Adding or expanding food/vitamin data |
| `ui-builder` | index.html UI | Building or changing any visual component |
| `pwa-specialist` | manifest.json / sw.js | PWA install, offline, service worker |
| `log-engine` | localStorage / logging JS | Logging, totals, history, data persistence |
| `qa-reviewer` | Read-only QA | After any feature — test before shipping |
| `bug-fixer` | bugs.json | Fix logged bugs by priority |

## Current Build Status

### ✅ Complete
- [x] Project structure and working directory established
- [x] CLAUDE.md — project memory (this file)
- [x] .claude/agents/ — 6 specialist agents
- [x] bugs.json — persistent bug backlog (active, 3 bugs logged)
- [x] foods.json — 68 preloaded items
      (Kirkland proteins, dairy, nuts, snacks, frozen, beverages,
      user daily foods: coffee, honey, 1% milk, apple, string
      cheese, golden kiwi, Barebells bars + Costco staples)
- [x] vitamins.json — 20 Kirkland supplement entries
- [x] rdas.json — Harvard RDA/AI table, 30 nutrients,
      personalized by age + sex
- [x] manifest.json — PWA manifest (installable)
- [x] sw.js — service worker v5
      (caches all assets including rdas.json, offline-ready)
- [x] index.html — food browser with brand+name search
- [x] index.html — manual nutrition entry form
- [x] index.html — daily log view with macro totals card
- [x] index.html — supplement tab (browse, search by
      name+brand, log modal, unverified badge, today summary)
- [x] index.html — micronutrient detail view (#microView)
      (VITAMINS / MINERALS / OTHER sections, progress bars,
      color-coded by status, AI vs RDA badge, UL warnings)
- [x] index.html — micronutrient summary on totals card
      (critical/low/met badge counts, tap → #microView)
- [x] index.html — upper limit warning toasts
- [x] index.html — bottom navigation
      (Foods | Log | 💊 Supplements | 🧬 Micros)
- [x] index.html — personalized goals onboarding page
- [x] index.html — height input: ft + in fields
      (converts to totalInches for storage, backward compatible)
- [x] index.html — getUserRDA() personalized by age + sex
- [x] index.html — getDailyMicronutrientTotals()
- [x] index.html — getMicronutrientStatus()
- [x] index.html — getMicronutrientSummaryForCard()
- [x] index.html — exportAllDataAsJSON() v2.0
      (exports ALL localStorage keys: profile, goals,
      log, preferences — not just logEntries)
- [x] index.html — getWeeklyMacroSummary()
      (7-day lookback, ready — not yet wired to chart UI)
- [x] index.html — addLogEntry() fixed (bug_003)
      (now copies all 30 micronutrient fields, not just 7 macros)
- [x] index.html — brand+name search (both fields searched)

### 🔲 Pending (priority order)
- [ ] iPhone home screen install + real device test
      (Option C — foundation is solid, needs on-device verify)
- [ ] Supplement → micronutrient wiring
      (add nutrientKey + nutrientUnit to vitamins.json,
      update getDailyMicronutrientTotals() to include supplements,
      closes gap where D3/fish oil/magnesium don't affect micros view)
- [ ] 17 micronutrient fields need food data backfill
      (thiamin, riboflavin, niacin, B6, B12, choline, selenium,
      zinc, etc. — top priority foods: eggs, salmon, spinach,
      chicken breast, beef)
- [ ] Weekly macro chart UI
      (getWeeklyMacroSummary() is ready, needs ui-builder pass)
- [ ] Calcium upper limit age-split correction in rdas.json
      (currently flat 2500mg, should be 2000mg for age 51+)
- [ ] Custom food entry form improvements
- [ ] Photo-based nutrition scanning
      (Phase 2 — requires API integration, out of scope for local PWA)

### 🐛 Bug Backlog (bugs.json)
- bug_001 ✅ FIXED — exportAllDataAsJSON() missing profile/goals
- bug_002 ✅ FIXED — 20 foods.json items missing category field
- bug_003 ✅ FIXED — addLogEntry() only copied 7 macro fields,
  silently zeroed all 30 micronutrient fields on every log entry

### Known Gaps (not bugs, design decisions to revisit)
- Supplement entries in the log don't contribute to micronutrient
  totals yet (by design — needs nutrientKey wiring first)
- Magnesium upper limit note (supplements only) logged in rdas.json
  but not yet enforced differently in UL warning logic
- QA qa-reviewer audit of micronutrient feature not yet run
  (getUserRDA() key-name match with profile page unverified)

**How to invoke:** Just describe the task and Claude will route to the right agent.
Or be explicit: "Use the qa-reviewer to check the app" / "Use data-builder to add salmon."
