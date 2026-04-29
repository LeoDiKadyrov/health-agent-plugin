---
name: add-result
description: Interactively add a new lab result, medical report, or doctor consultation note to your health data files.
disable-model-invocation: true
---

## Setup

Read `health.config.json`. If missing, tell user to run `/onboard` first.

Extract from config:
- `data.tests` — directory for lab result files
- `data.reports` — directory for medical report files

## Step 1: Ask what type of result to add

Present options:
1. **Lab results** (кровь, спирометрия, моча, другое) — appends to `data.tests` directory
2. **Medical report / doctor note** — appends to `data.reports` directory

Wait for user choice before proceeding.

---

## Branch A: Lab Results

### Step A1: Collect data sequentially

Ask each question and wait for the answer before asking the next:

1. Test type — e.g. "complete blood panel", "lipid panel", "spirometry"
2. Date — ask for YYYY-MM-DD format; accept natural dates and convert
3. Lab name — e.g. "ОЛИМП", "Инвитро", "Диацент"
4. Results — instruct: "Paste your results as plain text, a table, or a list of `marker: value unit` pairs (one per line). Include reference ranges if available."

### Step A2: Parse results

Accept any of these input formats:
- `marker: value unit` (e.g. `Глюкоза: 4.8 ммоль/л`)
- `marker | value | unit | ref` (pipe-separated)
- Free-form text (extract marker name, value, unit, reference range where present)

Map to table columns: Маркер | Значение | Единицы | Референс лаб

If a reference range is not provided for a marker, leave that cell empty.

Determine `type` field from test type:
- blood keywords → `blood_panel`
- spirometry / FVC / FEV keywords → `spirometry`
- imaging / УЗИ / рентген / МРТ keywords → `imaging`
- моча / urine keywords → `urine`
- otherwise → `other`

### Step A3: Determine target file

Check `data.tests` directory:
- If a file named `tests.md` exists there → append to it
- If no file exists → create `tests.md` in that directory

### Step A4: Append block

Append to the file (prepend `---` separator if the file already has content):

```markdown
---

## [Test Type] — [Lab Name] — [Date]

---
date: [YYYY-MM-DD]
lab: [lab name]
type: [blood_panel | spirometry | imaging | urine | other]
---

| Маркер | Значение | Единицы | Референс лаб |
|-|-|-|-|
[one row per parsed marker]
```

### Step A5: Confirm

Reply: "Added [N] markers from [Lab Name] [Date] to [relative path to file]."

---

## Branch B: Medical Report / Doctor Note

### Step B1: Collect data sequentially

Ask each question and wait for the answer before asking the next:

1. Date — YYYY-MM-DD; accept natural dates and convert
2. Doctor / specialty — e.g. "Пульмонолог", "Dr. Иванов", "Общая практика"
3. Report content — instruct: "Paste the consultation note, diagnosis summary, or any text from the visit."

### Step B2: Determine target file

Check `data.reports` directory:
- If a file named `reports.md` exists there → append to it
- If no file exists → create `reports.md` in that directory

### Step B3: Append block

Append to the file (prepend `---` separator if the file already has content):

```markdown
---

## [Doctor/Specialty] — [Date]

---
date: [YYYY-MM-DD]
doctor: [doctor name or specialty]
type: consultation
---

[Formatted report content — preserve paragraph structure, convert bullet lists if present]
```

### Step B4: Confirm

Reply: "Added consultation note ([Doctor/Specialty], [Date]) to [relative path to file]."

---

## Error Handling

- If `data.tests` or `data.reports` path from config does not exist as a directory: create it, then proceed.
- If date cannot be parsed: ask user to re-enter in YYYY-MM-DD format.
- If results input is empty or unparseable: ask user to re-paste with at least one `marker: value unit` line as an example.
- Do not overwrite any existing content. Always append.
