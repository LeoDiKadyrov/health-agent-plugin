---
name: plan-panel
description: Identify missing high-priority biomarkers and build an optimized lab panel with price comparison across available labs.
disable-model-invocation: true
---

## Setup

Read `health.config.json`. If missing, tell user to run `/onboard` first.

Read all Markdown files in `data.tests` directory (to know which markers already measured).
Read all `kb/static/*.md` files for protocols in `benchmarks` config (to know which markers are high-priority).

## Step 1: Gap analysis

For each high-priority marker in configured protocols: check if user has measured it in the last 6 months.

Build two lists:
- **Критично** — high-priority markers never measured or measured >12 months ago
- **Желательно** — medium-priority markers not measured in the last 6 months

## Step 2: Build panel

Group markers by lab test category (since labs bundle tests into panels):
- Lipid panel: ApoB, LDL-C, HDL-C, Triglycerides, Lp(a), non-HDL-C
- Glycemic: Fasting glucose, Fasting insulin, HbA1c
- Inflammation: hs-CRP, Homocysteine, Ferritin
- Liver: ALT, AST, GGTP, Bilirubin
- Kidney: Creatinine, eGFR, Cystatin C, Uric acid
- Thyroid: TSH, T3 free, T4 free
- Hormones (male): Total testosterone, Free testosterone, SHBG, DHEA-S, LH, FSH
- Nutrients: Vitamin D 25-OH, Vitamin B12, Folate, Magnesium, Omega-3 Index
- CBC: Haemoglobin, WBC, Platelets, RBC

For each missing marker, assign to its test category. List required test categories.

## Step 3: Price comparison (if lab_prices configured)

If `lab_prices` is null: skip this step. Inform user: "Add `lab_prices: <city>` to health.config.json to enable price comparison."

If `lab_prices` is set (e.g., "karaganda"):

Find all YAML files in `labs/<lab_prices>/`. For each lab:
1. Read the YAML config to get the URL
2. Fetch the price page using WebFetch
3. Parse the HTML table using the column indices from the YAML (name_column, price_column, html_class if set)
4. Extract prices for all tests in the required panel

Build comparison table:

| Тест | Олимп | Инвитро | Диацент | DiVera |
|-|-|-|-|-|
| ApoB | 2 800 ₸ | — | 3 100 ₸ | 2 950 ₸ |
| HbA1c | 1 200 ₸ | 1 350 ₸ | 1 100 ₸ | — |

**Оптимальная корзина** — for each test, select the cheapest available lab. Show total cost.

Note if a test is not available at any local lab (user may need to order online or visit another city).

## Step 4: Output

### Отсутствующие маркеры

**Критично сдать:**
- [marker] ([protocol]) — последнее измерение: [date or "никогда"]

**Желательно сдать:**
- [marker] ([protocol]) — последнее измерение: [date]

### Рекомендуемые панели

List of test categories to order, with which markers each covers.

### Сравнение цен (if lab_prices configured)

Price comparison table as above.

**Оптимальная корзина:**
| Тест | Лаборатория | Цена |
|-|-|-|
| ApoB | Олимп | 2 800 ₸ |
...
**Итого: X ₸**

### Следующий шаг

One sentence: what to book and where, based on optimal basket.

## Price fetch rules

- Fetch lazily: only fetch a lab's price page if at least one required test might be there
- If WebFetch returns an error: note the lab as "недоступна" and continue with others
- Do not retry failed fetches more than once
- Note to user: "Prices fetched live. Check directly with lab before booking."
