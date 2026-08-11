# Pipeline Quick Reference

## How to run the pipeline

In Claude Code, say:

  "run the pipeline for [what you want to build or fix]"

The orchestrator agent handles everything:
plan → build → validate → QA → bug check → commit → push

## Pipeline stages
  1. INTAKE     — assess change, assign agents, check risk
  2. PLAN       — write agent briefs, check for blocking bugs
  3. BUILD      — dispatch specialist agents
  4. VALIDATE   — orchestrator self-checks output independently
  5. QA AUDIT   — qa-reviewer targeted audit → bugs.json
  6. BUG CHECK  — bug-fixer if critical issues found
  7. COMMIT     — staged files only, structured message
  8. PUSH       — master → GitHub Pages (~60s to go live)

## Risk levels
  LOW      → auto-proceeds (data file changes)
  MEDIUM   → auto-proceeds (JS logic changes)
  HIGH     → states intent, proceeds unless stopped
  CRITICAL → pauses and waits for user confirmation before build
             pauses again before commit

## What blocks a push
  - Invalid JSON in any data file
  - Open CRITICAL bug in touched files
  - qa-reviewer returns 🚨 CRITICAL
  - git push conflict or auth error
  - Any agent fails same task twice

## Dry run (plan only, no build)
  "dry run pipeline for [feature]"

## Check current status
  "pipeline status"

## Validate without rebuilding (change already made manually)
  "validate and push [description of what changed]"

## Agents in the pipeline
  data-builder   → JSON data files
  ui-builder     → HTML/CSS in index.html
  log-engine     → JS functions in index.html
  pwa-specialist → sw.js and manifest.json
  qa-reviewer    → audits only, writes to bugs.json
  bug-fixer      → fixes from bugs.json backlog
  orchestrator   → YOU — coordinates everything above

## Live URL
  https://kooalaproductions.github.io/nutrient-tracker-pwa/
  (auto-rebuilds ~60s after push to master)
