# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

Claude Code plugin providing health analytics skills. Users run skills like `/analyze`, `/plan-panel`, `/add-result` against their personal health data. Skills compare lab results and training data to 5 longevity protocols: Blueprint, Medicine 3.0, AHA Life's Essential 8, Sinclair, WHO.

No build system. No tests. No package manager. Skills are Markdown instruction files — declarative, not executable.

## Skill Architecture

Each skill lives at `skills/<name>/SKILL.md` with YAML frontmatter:

```yaml
---
name: skill-name
description: shown in Claude Code skill picker
---
```

Skills are instruction sets that tell Claude how to process user data. They reference shared data paths from `health.config.json` (created by `/onboard`).

`plugin.yaml` — root manifest declaring all skills and plugin metadata.

## Data Contracts

All data formats are defined in `schema/data-conventions.md`. This is the **single source of truth** — both skills and the planned MCP server (v2) must conform to it.

- **Lab tests** (`data.tests`): Markdown files with YAML frontmatter + result tables
- **Medical reports** (`data.reports`): Markdown files with consultation sections
- **Training journal** (`data.training`): CSV, columns: `date, day_type, exercise, set_1–set_4, notes`
- **KB protocols** (`kb/static/*.md`): Markdown tables with markers, units, goals, priority

Training set format: `<weight>kg <reps>reps` or `skip`. Empty = not performed.

## Knowledge Base

`kb/static/` — one file per protocol with YAML frontmatter + marker table. Priority values: `high | medium | low`.

`kb/sources.yaml` — URLs for `--live` flag fetching of current protocol guidelines.

`labs/<city>/<lab>.yaml` — price data for live lab cost comparison in `/plan-panel`. `labs/template.yaml` defines the schema for community contributions.

## Config

`health.config.json` — user profile (DOB, sex), data directory paths, chosen benchmarks, `lab_prices.city`. Created interactively by `/onboard`. Skills read all data paths from here.

## Adding a Skill

1. Create `skills/<name>/SKILL.md` with frontmatter
2. Add entry to `plugin.yaml` under `skills:`
3. If skill needs new data format, extend `schema/data-conventions.md` first

## v2 Roadmap

MCP server will expose data-conventions paths as MCP tools (`read_tests`, `read_reports`, `read_training`, `read_kb`, `read_lab_prices`). Skills stay unchanged — they'll call MCP tools instead of reading files directly.
