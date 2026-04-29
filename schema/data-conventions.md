# Health Agent Data Conventions

All skills read data from paths declared in `health.config.json`. This document defines the expected format for each data type.

## Lab Tests (`data.tests`)

Directory containing Markdown files with lab results. Each file or each section within a file must have YAML frontmatter:

```yaml
---
date: YYYY-MM-DD
lab: Lab Name
type: blood_panel | spirometry | imaging | urine | other
---
```

Results follow as a Markdown table:

| Маркер | Значение | Единицы | Референс лаб |
|-|-|-|-|
| Глюкоза | 4.8 | ммоль/л | 3.9–6.1 |

Multiple studies in one file are separated by `---`.

## Medical Reports (`data.reports`)

Directory containing Markdown files with doctor consultation notes. Each consultation section starts with a header and YAML frontmatter:

```yaml
---
date: YYYY-MM-DD
doctor: specialty or name
type: consultation | follow-up | specialist
---
```

Multiple consultations in one file are separated by `---`.

## Training Journal (`data.training`)

CSV file with columns:

| Column | Format | Example |
|-|-|-|
| date | YYYY-MM-DD | 2025-10-01 |
| day_type | Upper/Lower/Pull/Push/Legs/Rest | Upper |
| exercise | Exercise name | Incline Barbell Press |
| set_1 | `<weight>kg <reps>reps` or `skip` | 60kg 8reps |
| set_2 | same | 60kg 8reps |
| set_3 | same | 60kg 9reps |
| set_4 | same or empty | |
| notes | free text | back pain |

## Knowledge Base (`kb/static/`)

Protocol files follow this format:

```yaml
---
protocol: blueprint
source: Bryan Johnson / Blueprint
version: 2024
priority_markers: [ApoB, LDL-C, HbA1c, hs-CRP, Omega-3 Index]
---
```

Followed by a Markdown table:

| Маркер | Единицы | Цель | Приоритет | Примечание |
|-|-|-|-|-|
| ApoB | мг/дл | <60 | high | Лучше LDL как предиктор |

Priority values: `high` | `medium` | `low`

## MCP Contract (v2)

This document is the shared contract. The MCP server (v2) will expose these same data paths as MCP tools:
- `read_tests(path)` → reads from `data.tests`
- `read_reports(path)` → reads from `data.reports`
- `read_training(path)` → reads from `data.training`
- `read_kb(protocol)` → reads from `kb/static/<protocol>.md`
- `read_lab_prices(city, lab)` → reads from `labs/<city>/<lab>.yaml`
