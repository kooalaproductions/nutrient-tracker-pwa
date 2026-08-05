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

**How to invoke:** Just describe the task and Claude will route to the right agent.
Or be explicit: "Use the qa-reviewer to check the app" / "Use data-builder to add salmon."
