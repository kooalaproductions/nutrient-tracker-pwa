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

**Every change goes through the orchestrator pipeline.** Regardless of how the
request is phrased ("fix X", "add Y", "can you..."), route it through the
stages documented in `.claude/agents/orchestrator.md` / `PIPELINE.md`: intake
(assess risk) → plan (check bugs.json/CLAUDE.md for conflicts) → build
(dispatch the right specialist agent(s), sequentially if they touch shared
files) → validate (re-read the actual diff yourself — do not trust an agent's
self-report) → QA (targeted qa-reviewer audit) → bug check (fix anything QA
flags) → commit → push. This is a process rule for whoever is driving the
session (there is no requirement that a subagent literally named
`orchestrator` executes it — that agent's tool grant has no `Agent` tool, so
it cannot itself dispatch other agents; running its playbook at the top level
achieves the same result and is the proven path, including catching two real
bugs at the validate/QA gates in past runs). Skipping stages because a change
"seems small" is exactly the failure mode this rule exists to prevent.

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
| `orchestrator` | Pipeline coordination | "run the pipeline for X" — sequences data-builder/ui-builder/log-engine/pwa-specialist, self-validates, then qa-reviewer → bug-fixer → commit → push. See `PIPELINE.md` |

## Current Build Status

### ✅ Complete
- [x] Project structure and working directory established
- [x] CLAUDE.md — project memory (this file)
- [x] .claude/agents/ — 7 specialist agents
- [x] bugs.json — persistent bug backlog (9 bugs logged, all fixed)
- [x] foods.json — 74 preloaded items
      (Kirkland proteins, dairy, nuts, snacks, frozen, beverages,
      user daily foods: coffee, honey, 1% milk, apple, string
      cheese, golden kiwi, Barebells bars + Costco staples; the
      original 20 "generic" entries — banana, chicken breast, egg,
      spinach, etc. — now carry real per-serving micronutrient data
      instead of macros-only; added pistachios, canned wild sockeye
      salmon, cooked lentils, low-fat cottage cheese, organic quinoa,
      and raw red bell pepper to close remaining nutrient-coverage
      gaps — see "2026-08-10 data pass" below; every entry also has
      gramsPerServing now, the anchor for unit-conversion logging —
      see "unit conversion" entry below)
- [x] vitamins.json — 20 Kirkland supplement entries
- [x] rdas.json — Harvard RDA/AI table, 30 nutrients,
      personalized by age + sex
- [x] manifest.json — PWA manifest (installable)
- [x] sw.js — service worker v6
      (caches all assets including rdas.json and html5-qrcode.min.js,
      offline-ready; version bumped v5->v6 when the barcode-scanning
      library was added, to force existing installed clients to
      precache it promptly rather than wait on incidental revalidation)
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
- [x] index.html — supplement → micronutrient wiring, fully complete
      (vitamins.json nutrientKey/conversionFactor landed with bug_005;
      this session added getDailyFoodMicronutrientTotals() +
      fromFood/fromSupplements per-nutrient fields on
      getMicronutrientStatus() rows, suppContributedToday on
      getMicronutrientSummaryForCard(), a 🥗/💊 source-breakdown line
      in #microView, and a 💊+🥗 indicator on the totals-card summary
      row — closes the gap where D3/fish oil/magnesium supplements
      didn't visibly affect the micros view)
- [x] bug_004 — full fix confirmed and documented
      (magnesium/niacin/folate/vitamin-E upper limits are correctly
      checked against supplement-only totals, not skipped — fixNotes
      updated to remove stale "TODO: supplement wiring phase" language)
- [x] 2026-08-10 data pass — foods.json micronutrient backfill
      (user reported logging a banana showed no potassium anywhere;
      root cause was the original 20 generic entries had zero
      micronutrient fields at all. data-builder backfilled real
      per-serving values across all 20, at each entry's existing
      serving size. First pass had a systemic bug — several
      non-gram-serving entries, e.g. spinach's "2 cups", had
      per-100g reference values copied in unscaled, overstating
      some fields up to ~2.5x; spinach's vitamin K/vitamin A were
      caught as the exact unscaled 100g figures. Sent back for a
      second pass; all values re-verified as
      100g-reference × serving-weight-ratio before shipping.
      Also added 6 new items — see foods.json entry above.
      Live-tested: banana → 422mg potassium, red bell pepper →
      152mg vitamin C, combined totals verified arithmetically.)
- [x] index.html — log foods in alternate units
      (gramsPerServing on every foods.json entry anchors a
      density-derived conversion system — never a universal
      cup-to-gram assumption, since a cup of oatmeal and a cup of
      milk weigh completely different amounts. getAvailableLogUnits()/
      convertLogQuantityToMultiplier() compute a food-specific
      multiplier that feeds the existing, untouched addLogEntry(), so
      macro/micronutrient math stays correct regardless of unit used.
      Log modal: amount input + unit dropdown (grams/oz/lb/cups/tbsp/
      tsp/ml/fl-oz, filtered to only what's safely convertible for
      that specific food) replaced the old +/-0.5 stepper. "Add a
      custom food" form: servingUnit is now a structured dropdown
      instead of free text, with gramsPerServing auto-computed for
      weight units and optional for volume/count units. Independently
      validated with 12 executable tests incl. an exhaustive check
      across all 74 foods; live-tested logging oatmeal in grams —
      83 kcal + all 9 micronutrients exactly matched hand-calculated
      halves of the native 1-cup serving.)
- [x] index.html — scan a barcode to add a food (camera + Open Food Facts)
      (first-ever camera feature and first-ever live external network
      dependency in this app. html5-qrcode.min.js vendored locally
      (not CDN-loaded) so the scanner UI stays usable offline; free,
      no-API-key lookup against Open Food Facts, $0 ongoing cost.
      fetchProductByBarcode()/mapOpenFoodFactsProductToFood() map a
      scanned product to this app's schema as a 100g/gramsPerServing:100
      food, plugging directly into the unit-conversion system with zero
      new scaling logic. Unit conversion built on empirically-verified
      API behavior (every OFF nutrient is normalized to grams
      internally regardless of label unit) rather than assumed —
      caught and fixed a real bug during validation: folate was
      originally mapped to the wrong OFF key ("vitamin-b9", which
      doesn't exist in real product data; real key is "folates",
      confirmed via a live lookup). Never auto-saves — pre-fills the
      existing "Add a custom food" form (plus a new optional brand
      field) for review, with a read-only preview of any micronutrients
      found. qa-reviewer caught a real CRITICAL bug pre-ship: a camera
      stream leak if Cancel was tapped before the start() promise
      resolved (bug_008, fixed same session). Live-tested end-to-end
      via a mocked decode (real camera hardware not available in the
      testing environment) — OFF lookup, form pre-fill, save, and
      logging-in-alternate-units all verified correct against live
      Open Food Facts data. Real on-device camera behavior still
      needs a physical-phone test — see Pending.)

### 🔲 Pending (priority order)
- [ ] Real on-device camera test for barcode scanning
      (the scan-to-form-prefill logic is fully verified against live
      Open Food Facts data via a mocked decode; the literal
      camera-viewfinder-to-barcode-detection step, which is entirely
      html5-qrcode's own responsibility, has not been exercised with
      real camera hardware — test on an actual phone, including a
      rapid-cancel tap to confirm bug_008's fix holds under a real
      camera permission prompt, not just the simulated timing this
      session tested)
- [ ] iPhone home screen install + real device test
      (Option C — foundation is solid, needs on-device verify)
- [ ] Weekly macro chart UI
      (getWeeklyMacroSummary() is ready, needs ui-builder pass)
- [ ] Calcium upper limit age-split correction in rdas.json
      (currently flat 2500mg, should be 2000mg for age 51+)
- [ ] OCR nutrition-label scanning
      (fallback for foods/supplements without a scannable barcode;
      scoped but not started — see PIPELINE.md-style discussion in
      session history: on-device OCR via a library like Tesseract.js,
      pre-filling the manual entry form same as the barcode flow,
      never auto-saving given inconsistent label-photo OCR accuracy)

### 🐛 Bug Backlog (bugs.json)
- bug_001 ✅ FIXED — exportAllDataAsJSON() missing profile/goals
- bug_002 ✅ FIXED — 20 foods.json items missing category field
- bug_003 ✅ FIXED — addLogEntry() only copied 7 macro fields,
  silently zeroed all 30 micronutrient fields on every log entry
- bug_004 ✅ FIXED — magnesium UL wrongly applied to food-sourced
  totals; now correctly scoped to supplement-only totals
- bug_005 ✅ FIXED — supplement log entries never contributed to
  micronutrient totals (status field was stale "open" until this
  session corrected it to match the already-shipped fix)
- bug_006 ✅ FIXED — micronutrient contributor toggle's touch target
  was 40px, below the app's 44px convention
- bug_007 ✅ FIXED — contributor item names had no flex/min-width,
  risking horizontal overflow on narrow viewports
- bug_008 ✅ FIXED — camera stream leak if barcode-scan Cancel was
  tapped before the start() promise resolved (guard checked a flag
  that only flips true after start() succeeds, skipping stop())
- bug_009 ✅ FIXED — barcode-scan buttons were ~38px, below the app's
  44px touch-target convention

### Known Gaps (not bugs, design decisions to revisit)
- QA qa-reviewer audit of micronutrient feature not yet run
  (getUserRDA() key-name match with profile page unverified)
- vit_005 (B-Complex), vit_019 (Men's Multi), vit_020 (Women's Multi)
  map nutrientKey to a single headline nutrient (e.g. B-Complex →
  vitaminb12_mcg) rather than null, since the schema can't represent
  a multi-ingredient product natively — documented per-entry in
  vitamins.json notes; means those three log entries only contribute
  to one tracked micronutrient each, not their full real-world profile

**How to invoke:** Just describe the task and Claude will route to the right agent.
Or be explicit: "Use the qa-reviewer to check the app" / "Use data-builder to add salmon."
