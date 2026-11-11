# Economy Model Review

Published artifact: https://claude.ai/artifact/2EM3wAWnr4NCgWRBBwFKS5 (updated 11 Nov 2026 for the two-step cycle). Reproduced here for the repo.

The diagram titled "Willy Wonka Factory Trip" is a money-circulation model of a national economy. This page records what it contains, what happens when it runs, and how that compares with the real United States economy.

## How the model runs: a two-step cycle

The diagram is designed as a cycle of two steps. One full cycle is one economic period.

| Step | Direction | What moves |
| --- | --- | --- |
| Odd step: out | Money leaves the taxpayers | Households send 69% to Spending, 21% to Investment, 10% to the Import pool. Government spends through gate #1011 (Spending, Import pool, Interest, a little back to Households). |
| Even step: back | Money returns to people | Spending, Investment and Depreciation feed Business gate #496. It sends money through the AA and AAA tax gates to Households, and to Government. Government pays 1% to Households. |

Reading the model with this in mind:

- Household, Government and Business pools are expected to swing up and down every two steps. A swing with period 2 is the design, not a fault.
- Compare numbers only at the same phase (for example every even step) or as a two-step average. A reading at an odd step and one at an even step are different things.
- A pool that drains each step, such as the Interest pool #851, can read 0 at the end of one phase and full at the other. A reading of 0 does not prove nothing was paid.
- Per-step amounts are per half cycle. Borrowing of 2 per step is 4 per cycle.
- A real fault shows up as a drift of the same-phase value (falling or rising across cycles), money stuck in one pool, or a total that changes with no source.

This phase assignment is read from the structure of the diagram and Max's description. It has not been checked step by step in a Play.

## Result of the run

Play 676, 60 steps, before any fix: the economy does not circulate. Money leaves households and collects in the import pool.

- Household money 83 to 1
- 57 of 100 money stuck in the import pool
- Debt 95, unchanged
- 5 money reached World

After the import-pool fix (Play 677): import pool ends at 1, 0 runtime errors, 37 money leaked to World (30 trade, 7 interest), households end at 4.

After returning World money (Play 678): leakage 37 to 0, total money 100, households settle at 24. Closed and flat.

After government borrowing (Play 694): debt 95 to 155 over 30 steps, interest 0 to 7, BASE ends at 44 (about 142 in Play 688). Under the two-step design the BASE swing is expected. What still needs checking is the level: Plays 688 and 694 may have ended on different phases (30 steps is even, so both should end on the "back" phase; if so, 44 against 142 is a real difference).

## Findings

Each finding is classed as a real problem, the intended cycle, or a reading problem.

| Finding | Class | Status |
| --- | --- | --- |
| Import pool #836 push-all, two outputs computed from its own content one step late | Real problem | Fixed |
| State connections #936 and #1099 had no target | Real problem | Deleted |
| World money never returned (#642, #947) | Real problem | Fixed, Play 678 |
| No money entered the system | Real problem | Fixed, Play 694 (Borrowing) |
| Debt pool #709 had no flows | Real problem | Fixed, Play 694 |
| Overwrite on a %-weight gate output blocks it | Real problem | Fixed, gate #1011 plain weights |
| BASE oscillates every two steps | Intended cycle | Closed. Read at the same phase. |
| BASE ends at 44 instead of about 142 | Open question | Compare Plays at the same phase, then check whether it drifts across cycles |
| Interest pool #851 drains each step, so the end-of-step reading is 0 | Reading problem | Open. Add an Interest paid counter that accumulates. |
| Households 24 to 31 at the end | Reading problem | Compare at the same phase before judging the level |
| Depreciation flows back into Spending | Intended cycle | It returns in the out-step; not a loss of capital |
| Exports fixed at about 2/3 of imports | Real problem | Open (fix 6). Exports should arrive in the out-step so they are paid out in the back-step. |
| Stock pool #770 and Initial EXP are dead | Real problem | Open (fix 7) |
| Total subtracts export, which is derived from import | Real problem | Open (fix 7) |
| Register #640 reads the World pool as `export` | Real problem | Open (fix 7) |
| 46 elements in the unnamed layer | Real problem | Open (fix 7). Optional: group layers as Out step and Back step. |
| AI chat lists Treasury, Tax Collector, Tax Revenue as applied; none exist | Real problem | Open |
| Title and description are the tutorial; diagram public | Real problem | Open (fix 7) |

## Suggested checks for the two-step design

1. Add a Cycle register (step modulo 2) next to the TOTAL layer so every reading shows its phase.
2. Add an Interest paid counter and a Cycle total for Households, so one number per cycle can be compared.
3. Judge each fix by the same-phase value over a 30-step Play (15 cycles), not by the last step alone.

These are suggestions only. The diagram has not been edited for them.

## Real USA versus this model

Approximate shares from general knowledge, rounded, not looked up.

| Measure | Real United States | This model now | Match |
| --- | --- | --- | --- |
| GDP over time | About 2% real growth a year | New money enters; same-phase trend not yet checked | Partly |
| Household consumption | About 68% of GDP, stable | Households 31 at step 30 (Play 694), phase-dependent | Level unchecked |
| Private investment | About 18% of GDP | 21% of household outflow plus foreign inflow | Share only |
| Government purchases | About 17% of GDP | Spends everything it receives, plus borrowing | Partly |
| Imports and exports | About 14% and 11% of GDP | Import pool no longer builds up | Partly |
| Exports | Foreign demand | Fixed at about 2/3 of imports | No |
| Trade deficit | About 3% of GDP, returns as foreign investment | Returns to Investment and Rich | Yes |
| Government debt | Above 100% of GDP, grows by deficit | Grows 2 per step, 95 to 155 | Yes |
| Interest on debt | Proportional to debt | Gate weight scales with debt | Yes |
| Depreciation | About 16% of GDP | 16% of Business outflow, returned to Spending | Share only |
| Money supply | Grows through credit and borrowing | Grows through borrowing, 2 per step | Yes |

A correct run: same-phase household and GDP values flat or slowly rising across cycles; import register small and steady; debt rising by the deficit; interest rising with debt; World balance cycling back as export income and investment.
