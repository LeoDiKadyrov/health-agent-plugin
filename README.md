# health-agent-plugin

Open-source Claude Code plugin for AI-agent health analytics. Compares your lab results, medical reports, and training data against international longevity benchmarks (Blueprint, Medicine 3.0, AHA Life's Essential 8, Sinclair).

## Install

```bash
claude plugin install gh:yourusername/health-agent-plugin
```

## Quickstart

```
/onboard
```

Runs interactive setup. Creates `health.config.json` in your project and copies the knowledge base.

Then:

```
/analyze
```

Full health analysis against all configured protocols.

## Skills

| Skill | What it does |
|-|-|
| `/onboard` | First-run setup — creates config, copies KB |
| `/analyze` | Master analysis: gap-analysis, priorities, missing markers, training cross-analysis |
| `/compare-benchmark blueprint` | Compare against one protocol. Add `--live` for current guidelines |
| `/add-result` | Add new lab result or doctor consultation note |
| `/plan-panel` | Identify missing panels + live lab price comparison |

## Data Format

Your health data goes in directories you specify during `/onboard`. See `schema/data-conventions.md` for the expected Markdown and CSV formats.

## Supported Protocols

| Protocol | Source | Markers |
|-|-|-|
| Blueprint | Bryan Johnson | ~40 |
| Medicine 3.0 | Peter Attia | ~35 |
| AHA Life's Essential 8 | American Heart Association | 8 |
| Sinclair | David Sinclair | ~10 |
| WHO Reference | World Health Organization | ~20 |

## Karaganda Lab Prices

`/plan-panel` includes live price comparison for Karaganda labs: Олимп КДЛ, Инвитро KZ, Диацент, DiVera.

To add labs for your city: copy `labs/template.yaml`, fill in the URL and column indices, submit a PR.

> **Note on scraping:** Price comparison fetches publicly accessible HTML price pages (no authentication). Verify your lab's robots.txt and Terms of Service before use.

## Community Extension

- **New protocol:** Add `kb/static/<name>.md` following the format in existing files. Submit a PR.
- **New city labs:** Add `labs/<your-city>/<lab>.yaml` using `labs/template.yaml` as a guide.
- **New skill:** Add `skills/<name>/SKILL.md`.

## MCP Server (v2)

Coming in v2. The same `health.config.json` paths will be exposed as MCP tools — skills will work unchanged.

## License

MIT
