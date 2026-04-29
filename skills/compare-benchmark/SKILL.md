---
name: compare-benchmark
description: Compare your health data against a single longevity protocol. Args: blueprint | attia | aha_life8 | sinclair | who | all. Add --live to fetch current guidelines from official sources.
disable-model-invocation: true
---

## Arguments

Parse the skill invocation arguments:
- First argument: protocol name — one of `blueprint`, `attia`, `aha_life8`, `sinclair`, `who`, `all`
- Optional flag: `--live` — fetch current data from `kb/sources.yaml` URLs instead of static files

If no argument provided: ask the user "Which protocol? (blueprint / attia / aha_life8 / sinclair / who / all)"

## Setup

Read `health.config.json`. If missing, tell user to run `/onboard` first.

Read all Markdown files in `data.tests` directory.

## Protocol data source

**Without `--live`:** Read from `kb/static/<protocol>.md`. If protocol is `all`, read all files in `kb/static/`.

**With `--live`:** Read `kb/sources.yaml`. For the requested protocol, fetch the primary URL using WebFetch. Extract current biomarker targets from the fetched content. Note to the user: "Fetching live data from [URL]. Results may differ from curated static KB." Cache the fetched content for this session.

## Output

### Сравнение: [Protocol Name]

Single table covering all groups in the protocol:

| Группа | Маркер | Ваше значение | Дата | Цель | Статус |
|-|-|-|-|-|-|
| Липиды | ApoB | 72 мг/дл | 2025-10-01 | <60 | ⚠ |

### Приоритеты

Numbered list of all ✗ and ⚠ markers with specific recommended actions.

### Вывод

Two sentences: how well user matches this specific protocol, and the single most impactful action.

## If protocol is `all`

Run the comparison for each protocol sequentially. At the end, add a **Сводная таблица** showing status across all protocols for each marker:

| Маркер | Значение | Blueprint | Attia | AHA LE8 | Sinclair |
|-|-|-|-|-|-|
| ApoB | 72 мг/дл | ⚠ | ⚠ | — | — |
