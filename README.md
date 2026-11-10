# economyGDP

A [Machinations](https://machinations.io) diagram that simulates the money cycles of the USA economy.

One resource (money) circulates between households, business, government, investment, imports/exports and the rest of the world. Percentages for each split come from a Google Sheet variable called `economy`, so the shape of the economy can be tuned in the sheet without redrawing the diagram. The goal is a run where GDP stays flat or grows slowly, debt grows with deficits, and trade money returns, like the real USA. Today the model does not reach that yet; see the open fixes below.

## Status

- Diagram (Machinations): originally titled "Willy Wonka Factory Trip" (tutorial leftover), 60 steps, interval 5.
- Import-pool blockage fixed (Play 676 to 677). Households still fall to about 4 because 37 of 100 money leaks to World and nothing returns.
- Fix 4 (return World money) applied 2026-11-10: total money stays 100 and households end at about 24 in Play 678.
- Open fixes 5 to 7 are planned in [docs/fix-plan.md](docs/fix-plan.md). They are not applied.

## Contents

| Path | What |
| --- | --- |
| [docs/model.md](docs/model.md) | Entities: pools, gates, registers, connections, layers, values |
| [docs/economy-model-review.md](docs/economy-model-review.md) | Review of the model against the real USA economy, with Play results |
| [docs/fix-plan.md](docs/fix-plan.md) | Plan for root causes 4 to 7 |
| [CHANGELOG.md](CHANGELOG.md) | Log of changes made to the diagram |
| [snapshots/](snapshots/) | Dated exports of the diagram (see its README) |

## Getting the diagram back

There is no documented file export or import for Machinations diagrams. Two routes exist, both described in [snapshots/README.md](snapshots/README.md): Google Sheets export (CSV, re-importable) and the raw diagram JSON/XML the app loads from `/diagram/open/<id>` (undocumented, about 70 KB, no documented way to import it back).
