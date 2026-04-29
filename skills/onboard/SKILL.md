---
name: onboard
description: Interactive first-run setup for health-agent-plugin. Creates health.config.json in your project and copies the knowledge base. Re-runnable to update config.
disable-model-invocation: true
---

Set up the health-agent-plugin for this project. Follow these steps exactly.

## Step 1: Check existing config

Check if `health.config.json` exists in the current project root.

If it exists: inform the user ("Found existing health.config.json. Re-running onboarding will overwrite it. Continue? [y/n]"). Stop if they say no.

## Step 2: Ask profile questions

Ask the user these questions one at a time. Wait for each answer before asking the next.

1. **Date of birth** — format YYYY-MM-DD. Example: 2002-01-30
2. **Biological sex** — male / female
3. **Lab test files location** — path to directory containing your Markdown lab results. Press Enter to skip (can add later). Default: `medical_tests/`
4. **Medical reports location** — path to directory with doctor consultation notes. Default: `medical_reports/`
5. **Training journal** — path to CSV training log file. Press Enter to skip. Example: `Training/Training_journal.csv`
6. **Which benchmarks to use?** (comma-separated, or "all"):
   - `blueprint` — Bryan Johnson Blueprint
   - `attia` — Peter Attia Medicine 3.0
   - `aha_life8` — AHA Life's Essential 8
   - `sinclair` — David Sinclair
   - `who` — WHO standard references
   Default: `blueprint,attia,aha_life8`
7. **Lab prices** — city name for lab price comparison. Currently available: `karaganda`. Press Enter to skip.

## Step 3: Write health.config.json

Write the following JSON to `health.config.json` in the project root, substituting user answers:

```json
{
  "data": {
    "tests": "<answer to question 3, or null if skipped>",
    "reports": "<answer to question 4, or null if skipped>",
    "training": "<answer to question 5, or null if skipped>"
  },
  "benchmarks": ["<each benchmark from question 6>"],
  "profile": {
    "dob": "<answer to question 1>",
    "sex": "<answer to question 2>"
  },
  "lab_prices": "<answer to question 7, or null if skipped>"
}
```

## Step 4: Copy knowledge base

Find the plugin's `kb/` directory. It is located in the same directory as this SKILL.md file, two levels up (i.e., `../../kb/` relative to this file's location in `skills/onboard/`).

Copy the entire `kb/` directory to the project root. If `kb/` already exists in the project root, inform the user and ask: "Overwrite existing kb/? [y/n]"

The project root should now have `kb/static/blueprint.md`, `kb/static/attia.md`, etc.

## Step 5: Confirm

Print a summary:

```
✓ health.config.json created
✓ Knowledge base copied to kb/

Profile: <sex>, born <dob> (age <calculated age>)
Benchmarks: <list>
Data paths:
  Tests: <path or "not configured">
  Reports: <path or "not configured">
  Training: <path or "not configured">
Lab prices: <city or "not configured">

Run /analyze to start your health analysis.
```
