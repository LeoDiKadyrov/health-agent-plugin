---
name: analyze
description: Master health analysis. Reads all your health data and compares biomarkers, training, and reports against all configured longevity benchmarks. Outputs prioritized gap-analysis.
disable-model-invocation: true
---

## Setup

Read `health.config.json` from the project root. If it doesn't exist, tell the user to run `/onboard` first and stop.

Also read `schema/data-conventions.md` from the plugin root (same directory as `kb/`) for data format reference — needed before parsing training CSV.

Read the following files:
1. All Markdown files in `data.tests` directory (lab results)
2. All Markdown files in `data.reports` directory (if path configured)
3. `data.training` CSV file (if path configured)
4. All `kb/static/*.md` files that match the `benchmarks` list in config

If `data.tests` path is missing or directory is empty: inform user ("No lab results found at <path>. Add Markdown files following data-conventions.md format.") and stop.

## Output

### 1. Сравнение биомаркеров

For each benchmark in `benchmarks`, compare the user's latest value for each marker against the protocol's **Цель** column.

Status legend:
- ✓ = within longevity target
- ⚠ = within lab reference range but outside longevity target (optimization zone)
- ✗ = outside lab reference range (clinically significant)
- — = no data

Use the most recent value when a marker appears in multiple test files. Always compare against **Цель** (longevity target), not the lab's reference range. For the `who_reference` protocol, the column is named **Норма** instead of **Цель** — treat **Норма** as the comparison target.

Output one table per benchmark group:

**[Protocol Name] — [Group Name]**

| Маркер | Значение | Единицы | Цель | Статус |
|-|-|-|-|-|
| ApoB | 72 | мг/дл | <60 | ⚠ |

### 2. Приоритетные действия

List all markers with ✗ or ⚠ status, sorted by priority (high → medium → low from KB file).

Format:
```
N. **Маркер** [Protocol]: значение X → цель Y. Конкретное рекомендуемое действие.
```

### 3. Отсутствующие маркеры

List high-priority markers from all configured protocols that have no data (— status).

Split into two levels:
- **Критично** (priority: high) — measure these next panel
- **Желательно** (priority: medium)

### 4. Кросс-анализ тренировки ↔ биомаркеры

Only if `data.training` is configured and CSV is non-empty.

Parse the training CSV (see data-conventions.md for column format).
Identify patterns that intersect with biomarker findings:
- Low energy / high training volume → check testosterone, ferritin, B12
- High inflammation markers → correlate with training frequency and recovery
- Glycemic markers outside target → correlate with training intensity and timing

One paragraph per intersection found. Concrete and specific (not generic).

If the training CSV `notes` column contains pain/injury keywords (pain, sick, tired, skip, jitsu, allergy, trip) — flag them in this section.

### 5. Итоговая оценка

One paragraph: overall health pattern, strongest risk factor, main priority for next 90 days.
State which protocols were used for comparison.

## Rules

- ApoB takes priority over LDL-C. If ApoB is present, do not separately flag LDL-C.
- For markers present in multiple protocols with different targets: show the marker once with the strictest target, and note the source protocol in parentheses. Example: `| ApoB | 72 | мг/дл | <60 (Blueprint) | ⚠ |`
- Use latest value per marker across all test files.
- Training CSV required columns for cross-analysis: `date`, `day_type`, `exercise`, `set_1`–`set_4`, `notes`. If a column is absent, skip that analysis point silently. Do not error — partial data is valid.
- If training CSV notes column contains pain/injury keywords (pain, sick, tired, skip, jitsu, allergy, trip) — flag them in cross-analysis.
