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
- [x] bugs.json — persistent bug backlog (10 bugs logged, all fixed)
- [x] foods.json — 121 preloaded items
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
      see "unit conversion" entry below. 2026-08-11: added Perdue
      chicken nuggets, Kirkland ketchup, Kirkland salted mixed nuts,
      white rice, and Kirkland sour cream — caught a real error in
      white rice's first draft (macro/micronutrient values 2-10x too
      low for "enriched" rice despite the notes claiming otherwise)
      by cross-checking live against USDA FoodData Central before
      shipping, not by trusting the draft's stated confidence.
      2026-08-13: added 42 common household staples the dataset was
      missing entirely — fresh produce (tomato, onion, garlic,
      cucumber, romaine, corn, green beans, mushrooms, potato, grapes,
      orange, strawberries, blueberries), everyday proteins (bacon,
      ground turkey, pork chop, deli ham, canned tuna, plain rotisserie
      chicken, tilapia), basic dairy (butter, American cheese, cream
      cheese, parmesan, 2% milk, almond milk), grains (pasta, flour
      tortilla, bagel, corn flakes, saltines), condiments (mayo,
      mustard, ranch, marinara, olive oil), snacks (chips, popcorn,
      pretzels, a plain granola bar), and 2 beverages (cola, a sports
      drink) — distinct ids used wherever a concept already existed in
      another form (orange vs. the existing orange-juice entries,
      olive-oil vs. avocado-oil/coconut-oil, strawberries/blueberries
      fresh vs. the existing frozen entries, rotisserie-chicken vs.
      the existing "chicken salad, rotisserie style" entry))
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
      Open Food Facts data. 2026-08-12: user confirmed real-device
      scanning works on their phone — the camera opens, detects a real
      barcode, and finds/pre-fills a product correctly. bug_008's fix
      (camera-stream-leak-on-rapid-cancel) not separately re-verified
      via an explicit rapid-tap test on-device, but the user's security
      question about the camera indicator turning off was answered by
      walking through the actual stop()-on-decode/cancel/navigate-away
      code paths — no report of the camera staying active.)
- [x] index.html — log-modal default unit means "servings" (bug_010)
      (user-reported with a screenshot: the default unit option was
      labeled with the food's raw servingSize + servingUnit, e.g.
      "240 ml (1 cup)" for milk, right next to an Amount field
      defaulting to that same "240" — looked like "log 240 cups" even
      though the math was already correct. Redesigned so the default
      unit always means "number of servings": label is now always the
      plain word "servings", Amount defaults to 1, and
      convertLogQuantityToMultiplier()'s native case is now `amount`
      directly instead of `amount / food.servingSize`. The default
      "open modal, tap Log it" result is unchanged (still multiplier 1)
      — verified across all 79 foods via 10 executable tests. Live-
      tested on the exact milk case from the screenshot plus a 2-cup
      food (spinach); both now read Amount:1/Unit:servings correctly.)
- [x] index.html — manual micronutrient entry on custom foods
      (closes the gap where custom foods added via the manual form had
      zero micronutrient data, unlike foods.json/barcode-scanned items.
      A small fixed set of 8 nutrients users care about most — Vitamin D,
      Vitamin B12, Calcium, Iron, Magnesium, Potassium, Vitamin C, Zinc —
      chosen over a full 30-field form (user picked this scope explicitly).
      New collapsible "Add micronutrients (optional)" section in the
      custom-food form, reusing the existing toggle-row/form-body pattern;
      sparse-write on save (only real positive values are stored, matching
      the app's established convention everywhere else); barcode-scan
      pre-fill now writes these 8 keys directly into the editable inputs
      instead of the read-only-only preview, auto-expanding the section,
      and excludes them from the read-only "Also found via scan" list to
      avoid duplicate display. qa-reviewer independent audit: no findings.
      Live-tested end-to-end: saved a custom food with all 8 fields
      filled, logged it, and confirmed all 8 nutrients appeared correctly
      in the Micros view with correct values, percentages, and a working
      "Show 1 item" contributor breakdown pointing back to the food.)
- [x] index.html — tap a macro tile to see its contributors
      (mirrors the Micros view's existing "which items contributed to this
      nutrient" breakdown onto the Today's totals card — Calories, Protein,
      Carbs, Fat, Sugar, Fiber, Sodium. Design choice, confirmed with the
      user: unlike the Micros page's full-width rows, the totals card's
      .stat tiles are narrow grid cells (~74-160px), too cramped for an
      inline expand, so tapping a tile opens a small modal instead, reusing
      the same .micro-contributor-item markup and the app's existing
      title+X-close-button modal pattern. The underlying per-entry
      contributor lookup (previously getMicronutrientContributors(), used
      only by the Micros view) was generalized and renamed to
      getContributorsForKey() -- it already worked generically off any
      log-entry key, but needed one addition to be correct for macros: a
      `amount > 0` filter, since NUTRIENT_KEYS (unlike sparse micronutrient
      fields) are always defaulted to 0 on every entry, so without the
      filter every food logged today would show up as a "0g fiber"
      contributor to every macro it doesn't have. qa-reviewer independent
      audit: no findings, including confirming the filter is a no-op for
      the existing micronutrient caller and that the read-only History
      modal's totals tiles (a different, untouched function,
      totalsGridHtml()) correctly stay non-interactive. Live-tested end-to-
      end (including working around a real service-worker cache-staleness
      false alarm mid-test, resolved via the documented cache-clear
      sequence): logged two foods, tapped Protein/Calories/Carbs/Fat tiles,
      confirmed correct totals, correct multi-item sort order, correct
      empty state ("Nothing logged yet.") for a macro with nothing logged,
      backdrop-click-to-close, and confirmed zero regression on both the
      History modal's totals tiles and the Micros view's own contributor
      breakdown.)
- [x] index.html, goals.json, foods.json — Nutrition Coach: smart
      suggestions, meal planning & personalized goals
      (built via the full orchestrator pipeline, data-builder →
      log-engine → ui-builder, sequential, HIGH risk — paused for
      user confirmation before commit. goals.json: 7 health-goal
      profiles (weight loss, hair/skin, muscle gain, energy, bone
      health, gut health, overall wellness); foods.json: nutritionScore
      (1-10) + flags added to all 79 items. 10 new functions —
      getUserTDEE() (Mifflin-St Jeor, reuses the existing
      LB_TO_KG/IN_TO_CM/ACTIVITY_MULTIPLIERS constants), getDailyTargets(),
      getMissingNutrients(), getSuggestedFoods(), generateMealPlan(),
      getCoachingInsights(), getOnboardingQuestions(),
      getFreeFoodDataSources(), getActiveGoals(), logMealPlanItems().
      New Coach tab (🧠, 4th bottom-nav item — the nav was 3 items
      before this, not 4 as originally assumed) with a daily briefing,
      missing-nutrient suggestions (tap-to-log via the existing
      openLogModal()), coaching insight cards, a meal-plan generator
      with Log This Meal/Regenerate/Save, and a collapsible data-quality
      section linking 5 free external nutrition databases. Goal
      selection (multi-select, max 3) added to both onboarding and
      Settings via a shared renderHealthGoalCards() helper.
      IMPORTANT NAMING NOTE: the new selected-goal-IDs array is stored
      under STORAGE_HEALTH_GOALS="nt_healthGoals" — NOT the pre-existing
      STORAGE_GOALS="nt_goals", which already held an unrelated,
      shipped feature (per-nutrient calorie/macro goal overrides,
      read by loadGoals()/getTodayGoalProgress()/the totals tiles).
      This collision was caught during orchestrator intake before any
      code was written; nt_goals is completely untouched.
      qa-reviewer audit (model bumped to sonnet for this pass): NEEDS
      FIXES → one warning (bug_013), no criticals. Hand-verified
      getUserTDEE() math, meal-plan calorie accuracy (within ±2.5%),
      the 2-slot-per-food cap, max-3 goal selection, and confirmed no
      regression on bug_003 (micronutrient snapshotting) or bug_010
      (servings-based multiplier) through the new logMealPlanItems()
      path. One scope simplification from the original spec:
      getOnboardingQuestions() returns advisory {question, why, action}
      cards, not interactive input-widget schemas — rendered as such
      rather than inventing form fields the function doesn't support.
      2026-08-13: added getSuggestedSupplements() — Complete Your
      Nutrients rows now show a secondary, visually distinct 💊
      supplement suggestion alongside the existing food suggestions,
      one per missing nutrient, tap-to-log via the existing
      openSupplementModal() (no new modal). Reuses isSupplementOnlyUL()/
      getUserRDA()/getDailySupplementMicronutrientTotals() to skip any
      candidate that would push a supplement-only-UL nutrient
      (magnesium, niacin, folate, vitamin E) over its real upper limit,
      and skips a nutrient entirely if any supplement targeting it was
      already logged today. "Food first — supplements can help close
      persistent gaps" caption frames this as optional. qa-reviewer
      targeted audit: PASS, no findings — UL math hand-verified against
      real data (magnesium's only product, 500mg, correctly blocked
      against the 350mg supplement-only UL; folate's 680mcg projected
      dose correctly allowed under the 1000mcg UL).
      2026-08-13 (same day, second pass): fixed two real usability gaps
      caught by user testing. (1) Coach's Complete Your Nutrients card
      wasn't live-refreshing — addLogEntry()/logSupplement()/
      logMealPlanItems() re-render six other views on every log action
      but were never wired to refresh the Coach tab too, so logging a
      suggested item required switching tabs away and back to see it
      reflected; fixed with the same guarded
      `if (coachView visible) renderCoachView()` pattern already used
      in 3 other places in the file. CRITICAL-risk (addLogEntry/
      logSupplement are named explicitly in this project's risk
      classification) — paused for user confirmation before build and
      reviewed as an exact 3-line diff before commit; qa-reviewer
      targeted audit: PASS. (2) Nutrients silently vanished from the
      list the moment they hit 90% with no acknowledgment — user chose
      a persistent "✅ Completed today: X, Y, Z" summary line (over a
      transient checkmark or leaving it as-is) via
      getCompletedNutrientsToday(), shown in both the empty and
      non-empty list states, omitted entirely when nothing's been
      completed yet.
      2026-08-13 (third pass): meal plan gets a session-only undo --
      Regenerate/Generate-again now stash the outgoing plan in
      coachPreviousMealPlan before overwriting coachCurrentMealPlan; a
      "↩ Back to Previous Plan" button appears once there's actually a
      previous plan (never on the very first generate) and swaps the
      two, so clicking it twice returns you to where you started.
      Each meal card also got a "🔗 Find a recipe" link -- a plain web
      search built from that meal's real item names, opened in a new
      tab. Deliberately NOT a recipe database or API call: this app is
      backend-less and offline-first by design, so a search link was
      recommended over either a large low-coverage static recipe
      dataset or an external API dependency, and the user agreed.
      2026-08-13 (fourth pass): fixed a real usability gap the user
      caught -- generateMealPlan()'s per-slot picking was fully
      deterministic, so clicking Regenerate with nothing else changed
      reproduced an identical plan. Fixed with exclusion-awareness
      (prefer a food not already in play, fall back to a repeat only
      when a slot genuinely has no other candidate) rather than
      randomization, which would have degraded the nutrient-coverage
      scoring. Extracted the per-slot logic into buildMealSlotItems()
      (shared by generateMealPlan() and the new regenerateMealSlot())
      per the user's own suggestion to also support swapping just one
      meal (Breakfast/Lunch/Dinner/Snack) independently -- each meal
      card now has its own "🔄 New [Slot]" button, and both per-meal
      swaps and whole-plan regenerates go through the same undo stash,
      so "Back to Previous Plan" covers both. qa-reviewer targeted
      audit of the refactor: PASS, no findings.
      2026-08-13 (fifth pass): added a "Hide Meal Plan"/"Show Meal
      Plan" toggle so a generated plan can be collapsed without being
      lost. Deliberately implemented as a plain display toggle on
      #coachMealPlanResult rather than a <details> wrapper (this
      app's usual collapsible pattern, used by "Improve Your Data")
      -- renderMealPlanResult() rewrites that container's innerHTML on
      every generate/regenerate/per-meal-swap/undo, which would reset
      a nested <details> open state each time. Since the toggle only
      touches .style.display and renderMealPlanResult() never does,
      collapse/expand state survives all four of those actions for
      free.
      2026-08-14: ran a readability/usability audit via ui-builder
      (review-only pass, no edits) covering onboarding clarity,
      navigation/discoverability, terminology consistency, cognitive
      load, error-proofing, empty states, visual hierarchy, and
      overall touch-friendliness. Fixed the 5 "quick win" findings:
      deleting a logged entry now confirms first (previously
      irreversible on a single mis-tap); the ⭐ priority-nutrient star
      is now explained inline; the "AI" (Adequate Intake) badge got
      its own solid-pill style instead of visually reusing the
      unrelated "custom food" tag's identical outline style; Settings'
      "Health goals" -> "Coach Focus Areas" and "Goal overrides" ->
      "Custom Nutrient Targets" (three different Settings concepts no
      longer all bare-named "Goal(s)"); onboarding now reassures users
      their data stays on-device. Text/markup/CSS only, no logic
      touched. 4 "bigger change" findings deferred, need real design
      thought before building: Coach front-loads everything expanded
      on first visit; Log tab and Coach tab show overlapping data with
      no visual link tying them together; DV% (supplements) and
      RDA-based percentages are shown side by side with nothing
      explaining they're roughly the same idea.
      Live-tested all 5 quick wins in-browser: caught and fixed a real
      gap in the AI badge fix that reading the diff alone missed — it
      only rendered in microRowHtml() (nutrients with real logged data
      today), never in the separate microNoDataRowHtml() path that
      renders the far more common "Not logged today" state, so aiOnly
      nutrients never showed the badge in practice on a typical day.
      Fixed by adding the identical badge to microNoDataRowHtml() too;
      re-tested live and confirmed on Pantothenic Acid/Biotin/Choline/
      Vitamin K. Other 4 fixes confirmed working as built.)

### 🔲 Pending (priority order)
- [ ] iPhone home screen install + real device test
      (Option C — foundation is solid, needs on-device verify)
- [ ] Coach tab: collapse sections by default on first visit
      (currently front-loads everything expanded — overwhelming with
      sparse/no data logged yet; from the 2026-08-14 readability audit)
- [ ] Visually tie Log tab's totals/micros cards to Coach's re-derived
      versions of the same data (currently read as disconnected;
      2026-08-14 readability audit)
- [ ] Explain DV% (supplements) vs RDA-based percentages are roughly
      the same idea (2026-08-14 readability audit)
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
- bug_010 ✅ FIXED — log-modal default unit/amount looked like it meant
  hundreds of servings (e.g. "Amount: 240, Unit: 240 ml (1 cup)" for
  milk) — user-reported with a screenshot; "native" unit now always
  means "servings" (amount defaults to 1) instead of a raw quantity
- bug_011 ✅ FIXED — new tappable Today's-totals .stat tiles (tap a
  macro tile to see contributors) had no min-height, falling short of
  the app's 44px touch-target convention; .stat[data-key] now sets
  min-height:44px
- bug_012 ✅ FIXED — same tappable tiles were plain divs, unreachable
  by keyboard/screen-reader; totalsTileHtml() now emits a real
  <button> (matches food-row/cal-day/recent-day-chip convention),
  giving native Enter/Space activation with no separate keydown handler
- bug_013 ✅ FIXED — goals.json was missing from sw.js's PRECACHE_URLS;
  added it (grouped with the other data files) and bumped CACHE_NAME
  v6 → v7 so installed devices pick up the change

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
