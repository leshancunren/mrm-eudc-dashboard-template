# MRM & EUDC Dashboard Template

A quarterly reporting template for a **Model Risk Management Committee (MRMC)** and **Board**, covering both the model risk program and the End-User Developed Computing (EUDC) program on one risk-appetite scorecard.

> **All data in this repository is invented sample data.** It does not describe any real institution, model inventory, finding or limit.

## What's inside

| Path | Contents |
| --- | --- |
| `dashboard/index.html` | Standalone dashboard. Opens in any modern browser, works offline (d3 v7.9.0 is embedded), makes no network calls. |
| `data/*.csv` | Six sample tables: `models`, `findings`, `euas`, `trend`, `appetite`, `matters`. |
| `docs/build-spec.md` | Build specification: data dictionary, business rules, KRI logic, page layout, Power BI data model and DAX measures. |
| `docs/getting-started-at-work.md` | Bringing the template into a company workspace: cloning, keeping data internal, AI-assistant prompts, quarterly run. |

## Dashboard sections

1. **Board summary:** seven KRIs rated red/amber/green against appetite limits, change vs prior quarter, and matters for the committee (decisions vs information).
2. **Model risk detail:** inventory by tier, validation status, residual-risk heat map, sortable inventory table, findings by age and severity, past-due findings, validation pipeline.
3. **EUDC detail:** EUA inventory by risk tier, high-risk control compliance, downstream use, business-unit table, attestation and overdue reviews.
4. **Five-quarter trends** for key indicators.

## Using your own data

The CSV data is embedded in `dashboard/index.html` inside six blocks marked `DATA:` near the end of the file. Replace the CSV text in each block with your own extract, keeping the column names listed in `docs/build-spec.md`. All tiles, charts and tables recalculate from those blocks.

- KRI limits and definitions: the `appetite` block.
- Quarter-end KRI history: the `trend` block (one row per quarter, oldest first; the KRI cards compare the last two rows).
- Committee items: the `matters` block.
- Colours and fonts: the design tokens in the first `<style>` block.

Printing hides navigation and search and prints tables in full, so the page can be saved as PDF for a committee pack.

## Rebuilding in a BI tool

`docs/build-spec.md` defines every field and rule so the dashboard can be rebuilt in Power BI, Tableau or Excel against live source systems. It includes a suggested Power BI model and DAX for every KRI.

## Governance note

If adopted for committee or Board reporting, the dashboard is itself an end-user application: register it, tier it, version-control it, and have its calculations independently reviewed against the build specification before use.

## License

MIT. See `LICENSE`. Third-party: d3 (ISC License, © Mike Bostock), embedded in `dashboard/index.html`.
