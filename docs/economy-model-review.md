# Economy Model Review

Published artifact: https://claude.ai/artifact/LoQuWK2y8LeACBJDTheSjd (10 Nov 2026). Reproduced here for the repo.

The diagram titled "Willy Wonka Factory Trip" is a money-circulation model of a national economy. This page records what it contains, what happens when it runs, and how that compares with the real United States economy.

## Result of the run

Play 676, 60 steps, before any fix: the economy does not circulate. Money leaves households and collects in the import pool.

- Household money 83 to 1
- 57 of 100 money stuck in the import pool
- Debt 95, unchanged
- 5 money reached World

After the import-pool fix (Play 677): import pool ends at 1, 0 runtime errors, 37 money leaked to World (30 trade, 7 interest), households end at 4. The blockage is gone; households run down because money keeps leaving and nothing returns.

## Findings

| Finding | Effect | Status |
| --- | --- | --- |
| Import pool #836 push-all, two outputs with amounts computed from its own content one step late | Pool blocked and accumulated; main cause of Play 676 collapse | Fixed |
| State connection #936 had no target | Runtime error | Deleted |
| State connection #1099 had no target | Runtime error | Deleted |
| No money enters the system | Any leak is permanent; economy only shrinks | Open (4) |
| Debt pool #709 has no flows | Debt fixed at 95; interest not tied to debt | Open (5) |
| Total subtracts export, which is derived from import | Not a true conservation check | Open (7) |
| Exports fixed at about 2/3 of imports | Not driven by foreign demand | Open (6) |
| Register #640 reads World pool as `export` | Misleading label | Open (7) |
| Stock pool #770, Initial EXP dead elements | Clutter | Open (7) |
| Depreciation flows back into Spending | A detour, not a loss of capital | Open |
| 46 elements in unnamed layer, several misplaced | Hiding a layer leaves gaps | Open (7) |
| AI chat lists Government Treasury, Tax Collector, Tax Revenue as applied | None exist in the saved diagram | Open |
| Title and description are the tutorial; diagram public | Misleading to Community viewers | Open |

## Real USA versus this model

Approximate shares from general knowledge, rounded, not looked up.

| Measure | Real United States | This model | Match |
| --- | --- | --- | --- |
| GDP over time | About 2% real growth a year | Decays toward 0 | No |
| Household consumption | About 68% of GDP, stable | Households 83 to 1 | No |
| Private investment | About 18% of GDP | 21% of household outflow, dies with households | Share only |
| Government purchases | About 17% of GDP | Spends everything it receives | Partly |
| Imports and exports | About 14% and 11% of GDP | Import money piles up (before fix) | No |
| Exports | Foreign demand | Fixed at about 2/3 of imports | No |
| Trade deficit | About 3% of GDP, returns as foreign investment | Leaves to World, never returns | No |
| Government debt | Above 100% of GDP, grows by deficit | Fixed at 95 | No |
| Interest on debt | Proportional to debt | Fixed 3.36% of government spending | No |
| Depreciation | About 16% of GDP | 16% of Business outflow, returned to Spending | Share only |
| Money supply | Grows through credit and borrowing | Fixed total | No |

A correct run: household and GDP lines flat or slowly rising; import register small and steady; debt rising by the deficit; interest rising with debt; World balance cycling back as export income and investment.
